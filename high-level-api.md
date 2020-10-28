<h1>A High-Level API</h1>

<h2>Table Of Contents</h2>

- [The `<map>` element](#the-map-element)
- [The `<layer>` element](#the-layer-element)
- [The `<extent>` element](#the-extent-element)
- [The `<input>` element](#the-input-element)
- [The `<tile>` element](#the-tile-element)
- [The `<feature>` element](#the-feature-element)
- [Tiled vector features](#tiled-vector-features)

<h3 id="the-map-element">The <a href="https://maps4html.org/MapML/spec/#the-map-element"><code>&lt;map&gt;</code></a> element</h4>

To allow authors to create declarative Web maps that have approximately the same authoring complexity as `<video>` or `<audio>` elements do today, we propose to extend the processing model of the currently existing HTML `<map>` element.  The current HTML `<map>` element, with its associated `<img>` and child `<area>` elements constitutes a simple Web map, wherein the pixel-based coordinate system of the `<img>` element is shared and used by the `<map>`’s child `<area>` elements to draw geometries to act as hyperlinks.  This relationship, that of parent node-child node with a shared coordinate system is an ideal extension point for more dynamic Web mapping. If it turns out that the code involved in the `<map>` element implementation is too much like spaghetti, we will have to create a new and similar element anyway.  So if possible we should avoid that particular form of technical debt and adopt, maintain and extend the existing `<map>` element.

![The map element](images/map-element.png "The map element")


<h3 id="the-layer-element">The <a href="https://maps4html.org/MapML/spec/#the-layer-element"><code>&lt;layer&gt;</code></a> element</h4>


A layer is a primitive abstraction that is shared by many if not most maps, both on and off the Web. Even in the case that a map doesn’t have more than a single layer, the map itself can be considered to be composed of that single layer. A key characteristic of map layers is that they share the same coordinate system as other layers in the map, allowing the rendering engine to lay out and render content over the same coordinate space.  This is in contrast to the normal rendering of HTML elements, which are laid out in document order down the page. There is precedent for the proposed rendering model for maps in HTML, however.  The [client-side image map](https://html.spec.whatwg.org/multipage/image-maps.html#image-maps) of HTML, implemented by the `<img>`, `<map>` and `<area>` elements, has this rendering model; the `<area>`s `coords` attribute are expressed in units of the pixel-based coordinate system of the `<map>`-associated `<img>` element.  An author-friendly system for dynamic Web mapping, such as that intended by this proposal, would make it as simple as possible to automatically layer map content, ideally re-using and extending the markup for `<img>` `<map>` and `<area>` that has become familiar.  A more thorough discussion of how this proposal conforms or relates to the HTML Design Principles is beyond the scope of this explainer, but is available [here](https://www.w3.org/community/maps4html/2019/12/09/the-design-of-mapml/).

<span id="bookmark1">![The layer element](images/layer-element.png "The layer element")</span>


Layers are added to maps by adding `<layer>` elements as children of the `<map>` element. Every `<layer>` added to the map must share the same coordinate system declared by the `<map>` in its `projection` attribute. If the layer added does not share the same coordinate system as the `<map>`, it will not be displayed and the layer will be ‘disabled’ in the layer control of the map widget. The set of possible coordinate systems is small, and the values enumerated by the MapML specification.  If no `projection` attribute is specified, the default coordinate system is that of Web Mercator, signified by the `OSMTILE` value. 

The `<layer>` element renders content by one of two alternate means.  The first is by referring to remote content at a URL specified by the `src` attribute. This is described under [Key Scenarios](README.md#key-scenarios) in the main explainer. The response to a request of the `src` value should be “MapML” content, identified over the network by the `text/mapml` media type.  MapML is intended to be part of the HTML vocabulary and to be parsed with the (almost) unmodified HTML parser.  To achieve that, some elements in the [MapML specification](https://maps4html.org/MapML/spec/) rely on and extend the definitions of elements of the same name in HTML. The second rendering path for `<layer>` is to include “MapML” content inline in light DOM. 

It’s great that we can imagine a way to extend HTML to include dynamic Web maps using almost-existing browser code infrastructure, but where will layer content as envisioned come from?  Without content, new browser code would not be useful.  What’s needed is a way to get the vast amount of existing content from services that have [well-defined standard and not-so-standard interfaces](https://www.geoseer.net/), into the new HTML-MapML format available at simple URLs, without requiring significant change to the content, but especially without requiring browsers to ‘understand’ interfaces other than the uniform interface of HTTP.  This might seem like a tall order, but a key design constraint followed by HTML-MapML is that the vocabulary itself should represent abstractions that ‘fit’ the existing content, so that if the services by which the content is accessed today were to provide HTML-MapML as a serialization format, then such content would be rendered inherently compatible with browsers, even before script execution.  Such experimentation has formed the basis of significant experimentation by several participants through the [OGC Testbed program](https://www.youtube.com/playlist?list=PLQsQNjNIDU85HBDZWc8aE7EvQKE5nIedK), and has proven to be readily possible in practice, through the creation of [server shims](https://docs.geoserver.org/latest/en/user/community/mapml/index.html).  We anticipate that should the Web community agree to work with the mapping community on this proposal, that we will have success in persuading the public spatial data infrastructures around the world to be made available to the browser in HTML-MapML.

<h3 id="the-extent-element">The <a href="https://maps4html.org/MapML/spec/#the-extent-element"><code>&lt;extent&gt;</code></a> element</h4>


Web maps’ content is, typically, but not exclusively,  provided by content servers associated with server-side Geographic Information Systems and their APIs.  These APIs often share common semantics associated to maps and map features, although typically with differing terminology between services. In order to access the widest variety of map services as possible, we created the  `<extent>` element, which can be a child of the `<layer>` element:


```html
<layer label="Canada Base Map" checked>
  <extent units="OSMTILE">
    <input name="z" type="zoom" value="17" min="0" max="17">
    <input name="y" type="location" units="tilematrix" axis="row" min="29750" max="34475">
    <input name="x" type="location" units="tilematrix" axis="column" min="26484" max="32463">
    <link rel="tile" tref="https://.../tile/{z}/{y}/{x}/">
  </extent>
</layer>
```


In order to decouple the client HTML vocabulary from server vocabularies, we use the pattern in HTML established by the `<form>` and `<input>` elements, which is a way to template the URL (in `<form>`s the URL is contained in the `<form action="">` attribute) to which the form submission occurs.  The `<extent>` element for map layers acts in a similar role to `<form>` in this decoupling, except that it establishes a two-dimensional rectangular context for server submissions, linked to the corresponding dimensions of the ancestor `<map>` element.  The `<extent>` submission events are generated automatically by the user events (pan or zoom) or user-agent actions (map resize).

<h3 id="the-link-rel=tile-or-image-tref-element">The <code>rel</code> and <code>tref</code> attributes</h4>


In HTML, the `<form>` element has the `action` attribute, whose value is a URL to which the form’s submission is directed, once action processing is completed.  We experimented with an `action` attribute on the `<extent>` element ([Issue #61](https://github.com/Maps4HTML/MapML/issues/61)), but we found it to be not as effective a mechanism for Web maps, for several reasons, including the fact that map services don’t always use URL query parameters, they sometimes also use URL path segments to identify server resources. So we followed the cow path established by the Leaflet and OpenLayers open source Web mapping libraries and added a proposal to extend the `<link>` element with map-specific link `rel` values, plus the `tref` attribute which contains a URL template.  

The processing model is similar, and related to that of `<form action="">`, in that just before submission, the extent is processed for inputs that specify named variables, whose values at the moment of submission are serialized into the URL template specified in the `tref` attribute, in the locations specified by their variable references, denoted by {varname}.  In this way, the actual varname values are decoupled from the specification, and are not required until submission.


```html
<input name="z" type="zoom" value="17" min="0" max="17">
...
<link rel="tile" tref="https://.../tile/{z}/{y}/{x}/">
```


The reason for specifying this as an extension of the `<link>` element is because there can be more than a single template processed within such an extent, retrieving image or document resources for each, and rendering the result in document order, for example, geometries and labels for a single “layer” may be rendered via different resource templates / links ([example](https://geogratis.gc.ca/mapml/en/osmtile/cbmt/)).

<h3 id="the-input-element">The <a href="https://maps4html.org/MapML/spec/#the-input-element"><code>&lt;input&gt;</code></a> element</h4>


There are four proposed types of input in MapML: `location`, `zoom`, `width` and `height`. Like inputs in HTML, each input has a `name` attribute which specifies the name of the variable to be set by the input.  This is the core mechanism whereby URLs processed by the browser are decoupled (or indirectly coupled, at least) from the services they are used to access; a server URL may be composed of names, the meanings of which are known to the service or domain, but which are meaningless to the browser. In this way, the client and service are directly coupled to the MapML specification, but decoupled from each other.

<h4 id="the-input-type-location-state">The <code>type="location"</code> state</h5>


Locations are specified in coordinates of one of the coordinate systems associated to the root “projection”, also known as a Tiled Coordinate Reference System, which is a term defined by MapML in order to unify concepts and vocabulary required for enabling maps in HTML. The location input has several attributes which authors specify to control the serialization of the location.  However, the coordinate systems of the Tiled Coordinate Reference Systems defined by MapML are widely understood even outside MapML; MapML mostly standardizes the terminology around those coordinate systems, especially the names,  into the specification for the purpose of using that terminology in the input attributes.  

<h6 id="the-input-type-location-units-attribute">The <code>units</code> attribute</h6>


Dynamic Web maps are pannable, zoomable and queryable through user gestures.  As a map is manipulated, it may require new content to cover its extent, or to respond to a query. To get new content, location events are generated for the extent implicated in the event (or a part of the extent, such as a map tile). Locations associated with the events are referenced by location inputs, by virtue of the combination of input attributes.  The `units` attribute has a small set of possible values, which can be one of `tcrs`, `pcrs`, `gcrs`, `map`, `tilematrix` or `tile`.  Each of these values signifies the child coordinate system from the parent [tiled coordinate reference system](README.md#tiled-coordinate-reference-systems) in which the event is considered to take place.  If the author specifies `units="tilematrix"`, for example, the location input refers to the location of each required tile as it is required. 

<h6 id="the-input-type-location-axis-attribute">The <code>axis</code> attribute</h6>


In the following example, the inputs with names ‘x’ and ‘y’ request the event location’s column and row coordinate axes, respectively. Note that while the location of the event has all three properties of a location in a tiled coordinate reference system, (i.e. zoom, horizontal axis value, and vertical axis value) the input is only able to refer to or serialize one property of the location.  Hence, the input carries the `axis` attribute, referring to the name of the horizontal axis (column, in the example below) and vertical axis (row, below) whose value is required.  The “zoom” property of a location event isn’t considered a coordinate axis, and zoom has an input type of its own. Nevertheless, a location event always has a zoom value.  ([Issue #56](https://github.com/Maps4HTML/MapML/issues/56))


```html
<extent units="OSMTILE">
  <input name="z" type="zoom" value="15" min="0" max="15">
  <input name="y" type="location" units="tilematrix" axis="row">
  <input name="x" type="location" units="tilematrix" axis="column">
  <link rel="tile" tref="https://.../tile/{z}/{y}/{x}/">
</extent>
```


All of the child coordinate systems of the parent tiled coordinate reference system exist in the same space; they are equivalent ways of referring to locations in the extent.  As such, when requesting a location’s coordinates, we aren’t limited to the coordinate axes of the child coordinate system identified in the `units` attribute, we could request any valid child coordinate systems axis. For example, if we want to specify the latitude of the tile event in the above example, we could have specified the `axis="latitude"`.  This returns the latitude of the center (by default) of the tile event. The set of legal axis names are: ‘x,y’ (from the tcrs pixel-based coordinate system), ‘i,j’ (from the map or tile pixel-based coordinate systems), ‘row,column’ (from the tile-based tilematrix coordinate system), ‘easting,northing’ (from the pcrs meter-based coordinate system), and ‘latitude,longitude’ (from the decimal-degrees-based gcrs coordinate system).

<h6 id="the-input-type-location-position-attribute">The <code>position</code> attribute</h6>


Location events can be relative to the rectangle of an extent or part of an extent.  The `position` attribute has a small set of possible values, which identify the location that is requested.  These are: `center` (the default), `top-center`, `bottom-center`, `top-left`, `top-right`, `bottom-left`, and `bottom-right`.  These named positions identify locations relative to a defined rectangle, such as the map extent, or the extent of a tile.  This allows the author to request locations that the user agent ‘understands’ as simply as possible. (Issue [#27](https://github.com/Maps4HTML/MapML/issues/27))

<h6 id="the-input-type-location-rel-attribute">The <code>rel</code> attribute</h6>


Location events generated by an extent can be located at a relative position of the extent, as identified by the `position` attribute.  The extent to which the event is related is by default the map extent.  By specifying the `rel="tile"` value, the position refers to the position on the tile in which the event is located.  This facility was designed to support querying of WMTS tiles, which require the pixel coordinates of the pixel within the tile in order to query the tile store.  ([Issue #57](https://github.com/Maps4HTML/MapML/issues/57))

<h6 id="the-input-type-location-min-and-max-attributes">The <code>min</code> and <code>max</code> attributes</h6>


The `<input type="location">`’s `min` and `max` attributes should be used together.  They are a way for the server to describe axis bounds that can be respected by the client.  When the client generates an event that results in a value for the input that is outside the established range (because the map is viewing a scene outside the range for this particular layer), no request will be made to URLs that require the variable to have a value.  In this way, the server can avoid needless traffic to itself by transmitting min/max on inputs for zoom and location axes.

<h4 id="the-input-type-zoom-state">The <code>type="zoom"</code> state</h5>

<h6 id="the-input-type-zoom-min-max-and-value-attributes">The <code>min</code>, <code>max</code> and <code>value</code> attributes</h6>


The `<input type="zoom">`’s `min` and `max` attributes should be used together.  They are a way for the server to describe the zoom limits that can be respected by the client, similar to the range established for axes limits by location inputs. The `value` attribute should be used to establish the zoom level at which axis bounds are value. 



In the following example, the zoom value is set at 17.  This means for example, that the row axis has a minimum value of 29750 and a maximum value of 34475 at zoom level 17.  The maximum and minimum values of the row axis at zooms 0 through 16 are calculated by transforming the value at zoom 17.  The reason for this is resolution.  Recall that at zoom level 0 some tiled coordinate reference systems may only be one tile by one tile, and so defining min and max in terms of tiles at zoom level 0 may not be precise enough to avoid redundant tile requests (that would result in 404 Not found).


```html
<extent units="CBMTILE">
  <input name="z" type="zoom" value="17" min="0" max="17">
  <input name="y" type="location" units="tilematrix" axis="row" min="29750" max="34475">
  <input name="x" type="location" units="tilematrix" axis="column" min="26484" max="32463">
  <link rel="tile" tref="https://.../tile/{z}/{y}/{x}/">
</extent>
```


For server-friendliness, it’s important for the author to establish the axis (and zoom)  bounds on a per-`<link>` template basis.  For example, there may be more than a single tile link template in an `<extent>`, with `<input>`s established to supply variable values to each one’s link template.  The reason might be to define a composite layer from different server image tile pyramids, for example.  One of the tile pyramids might not share the entire set of zoom levels with the other, nor indeed cover the same geographic extent (bounding box).  For this reason, separate zoom and axis bounds should be established by setting up different `<input>` variables to supply the different URL (`<link>`) templates, and thus generate only valid (200 OK) URLs.

<h3 id="the-tile-element">The <a href="https://maps4html.org/MapML/spec/#the-tile-element"><code>&lt;tile&gt;</code></a> element</h4>


The `<tile>` element doesn’t get used too much in practice.  Early on, we defined the `<extent action>` attribute, which made `<extent>` quite `<form>`-like, and which required that the server returned a MapML document with individual `<tile>` elements for each tile.  This meant that each zoom or pan (or resize), would require the server to generate and send a new MapML document, which would then be parsed and rendered by the client.  While functional, it turned out to be “chatty”, and unnecessary. ([Issue #61](https://github.com/Maps4HTML/MapML/issues/61))

Although the polyfill behaviour has not yet been thoroughly established through experimentation, the `<link rel="tile" ...>` element could be considered to generate `<tile>` elements in the DOM automatically, without having to parse a document containing such.  This will still allow scripted access to the DOM tiles, which will be important and useful, for example for accessing and manipulating map content on the client.

The `<tile>` element can also be used to serve static MapML content, for example to serve a directory of tiled images that have been statically generated.   An intelligent map client could cache these behind the scenes, and so provide better, perhaps even offline, performance for a map layer.

<h3 id="the-feature-element">The <a href="https://maps4html.org/MapML/spec/#the-feature-element"><code>&lt;feature&gt;</code></a> element</h4>


The `<feature>` element’s design mirrors what Web map developers actually do with vector information, which is commonly encoded in JSON as ‘[GeoJSON](https://tools.ietf.org/html/rfc7946)’.  The feature element has a `<properties>` child element, which is intended for human-readable HTML markup, rendered in its own browsing context such as a popup or pullout, and a `<geometry>` child element for the map-displayable feature content.  The need for a new `<geometry>` element is driven by the need to style and embed links in content, described below.
<span id="bookmark2"></span>

```html
<feature>
  <geometry>
    <multipolygon>
      <polygon>
         <a href="https://example.org/canada/mapml">
           <coordinates class="f72 outer">59.5303058 74.7915903 59.7266984 74.7479857 … 76.1838491 <span class="noline">67.5 76.1894504 67.5 76.7412725 67.5 77.0079535</span> 67.2115912 76.9522523 … 74.727385 59.5303058 74.7915903</coordinates>
         </a>
         <coordinates class="f72 hole">54.8127632 73.6650796 54.9487273 73.7169245 55.1327595 73.7134911 54.940487 73.6654229 54.8127632 73.6650796</coordinates>
       </polygon>
       <polygon>
         <coordinates class="f72 outer">58.7310016 75.895097 59.219923 75.946598 59.240523 75.995353 58.8079106 75.925311 58.7310016 75.895097</coordinates>
       </polygon>
     </multipolygon>
   </geometry>
   <properties>
     <table>
       <thead>
         <tr><th>Property name</th><th>Property value</th></tr>
       </thead>
       <tbody>
         <tr><th>scalerank</th><td>0</td></tr>
         <tr><th>featurecla</th><td>Admin-0 country</td></tr>
         <tr><th>labelrank</th><td>2</td></tr>
         <tr><th>sovereignt</th><td>Canada</td></tr>
         … 
         <tr><th>subregion</th><td>North America</td></tr>
      </tbody>
     </table>
   </properties>
</feature>
```


One of the use cases for modern Web maps is the application of CSS to feature styling.  In order to style feature vectors with CSS without duplicating them (duplication is semantically inappropriate, not to mention wasteful of resources), the parts must be selectable, and are thus represented as `<coordinates>` elements. In the case of `<polygon>`s, shown in the above example, the outer boundary ring is represented by the first child `<coordinates>` element, and interior holes in the polygon are represented by subsequent child `<coordinates>` elements.

The types of vector geometries for which there are proposed elements are:

`<point>`, `<linestring>`, `<polygon>`, `<multipoint>`, `<multilinestring>`, `<multipolygon>` and `<geometrycollection>`.  These types reflect the semantics of their counterparts in GeoJSON and most or all standards-based Geographic Information System vector formats.

<h3 id="tiled-vector-features">Tiled vector features</h4>


The selectability of features’ vector parts is useful in allowing servers to serve tiled or otherwise partitioned feature data without (necessarily) introducing visual or semantic ‘noise’.  The objective of doing so is often (but not exclusively) related to optimizing or minimizing bandwidth consumption; whereas tiled images can consume a lot of bandwidth, relatively (and depending on what content is being represented), tiled vector information can conserve bandwidth while conveying feature semantics both visually and through markup. In the screen capture below, tiled vector features (support simulated with SVG + CSS + JavaScript) are selected and styled in solid (random) colors and outlined in blue. They are displayed with no CSS rule to selectively style or hide ‘artificial’ vector segments introduced by tile boundaries.


```css
coordinates.f72.outer { stroke: blue; fill: #DAA520; }
coordinates.f72.hole { stroke: blue; fill: none; }
```

![Tiled vector data, tiles shown](images/vector-tiles-shown.png "image_tooltip")


In the next screen capture, below, a CSS selector for `span.noline` is introduced, which hides the tile boundaries.


```css
coordinates.f72.outer { stroke: blue; fill: #DAA520; }
coordinates.f72.hole { stroke: blue; fill: none; }
span.noline { display: none; }
```

![Tiled vector data, tiles hidden](images/vector-tiles-hidden.png "image_tooltip")


A [video demo of this feature](https://www.youtube.com/watch?v=ctGrhFgAONE) is available on YouTube. 