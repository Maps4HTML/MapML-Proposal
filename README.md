<h1>The MapML (Map Markup Language) explainer</h1>


<h2>Author</h2>


Peter Rushforth

<h2>Participate</h2>


The W3C [Maps for HTML Community Group](https://www.w3.org/community/maps4html/) is iterating on the problem space.  You can contribute to the on-going discussion and documentation of [Use Cases and Requirements for Web mapping](https://maps4html.org/HTML-Map-Element-UseCases-Requirements/). Alternatively, if your organization is a member of the [Web Platform Incubator Community Group](https://www.w3.org/community/WICG/) (WICG) and you are able to contribute there but not elsewhere, please consider contributing through the [WICG forum on Web mapping](https://discourse.wicg.io/c/web-mapping/22).  We would love to hear from you. 

[Issue tracker for this explainer](https://github.com/Maps4HTML/MapML-Proposal/issues)




<h2 id="introduction">Introduction</h2>


Web maps are a well-established domain of Web design, and there exist popular, mature open and closed source JavaScript libraries to create and manage Web maps.  JavaScript web maps are often containers for publicly available and funded open geospatial and statistical data.  Yet despite established JavaScript libraries and server-side API standards, Web maps remain a complex Web niche that is difficult to learn, due to their extensive prerequisite knowledge requirements. As a result, there exists a community of Web map developers which contributes very little to the Web platform and which may possess little understanding that the Web exists as a distinct and standards-based platform. Similarly, the Web platform seems mostly oblivious to Web maps and their requirements, and provides no direct support for maps. In other words, Web maps existence in the Web platform depends on intermediaries which “abstract away” the Web platform.  

The goal of this proposal is to bridge the gap between the two communities in a way that may have positive benefits for both sides. On the one hand, the Web mapping community is burdened by intermediaries and the consequent barriers to widespread creation and use of maps and public map information. On the other hand, the Web platform, especially the mobile Web, needs more and better high-level features and less JavaScript. Simple yet extensible Web maps in HTML, that equally leverage the other platform standards, is the feature that both communities need to come together to improve usability and accessibility for users.

<h2>Table Of Contents</h2>

- [The Problem](#the-problem)
- [The Proposal](#the-proposal)
- [Goals](#goals)
- [Non-Goals](#non-goals)
- [A High-Level API](#a-high-level-api)
- [Key Scenarios](#key-scenarios)
  - [Tiled Coordinate Reference Systems](#tiled-coordinate-reference-systems)
  - [Linking](#linking)
  - [Graceful Degradation and Progressive Enhancement](#graceful-degradation-and-progressive-enhancement)
  - [Polyfill](#polyfill)
- [Detailed design discussion](#detailed-design-discussion)
  - [Use Cases and Requirements](#use-cases-and-requirements)
- [Considered Alternative Designs of MapML](#considered-alternative-designs-of-mapml)
- [Alternative Approaches](#alternative-approaches)
- [Stakeholder Feedback / Opposition](#stakeholder-feedback--opposition)
- [References and Acknowledgements](#references-and-acknowledgements)

<h3 id="the-problem">The Problem</h3>


Web maps today are created using a wide range of technology stacks on both the client and server, some standard, some open, and some proprietary.  The complexity of choices and the wide variety of technologies required to create Web maps results in <a href="https://github.com/Malvoz/web-maps-wcag-evaluation/blob/master/README.md">maps of highly variable usability and accessibility</a>.  This has in turn led to the creation of centralized mapping services, that may or may not be implemented using Web technology; in some cases, mapping services which work well on desktop Web browsers mostly bypass the mobile Web through creation of mobile platform mapping apps, where the ‘rules of the Web platform’ (such as device permissions) do not apply.  Some centralized mapping services, both on the Web but especially on mobile technology platforms, are constructed for the purpose of tracking the user’s location and their locations of (search) interest, and using that private location information to market and re-sell highly targeted advertising. 

The problem to be solved, therefore, is to reduce the threshold complexity of creating accessible, usable and privacy-preserving Web maps, and to enable full use of Web platform standards such as HTML, URL, SVG, CSS and JavaScript in map creation, styling, presentation and interaction. 

<h3 id="the-proposal">The Proposal</h3>


To solve <a href="#the-problem">the problem</a>, our approach is to identify the Web map processing that is currently performed by JavaScript libraries which should instead be defined - in accordance with the <a href="https://www.w3.org/TR/html-design-principles/">HTML Design Principles</a> - as elements and attributes supported by CSS, while at the same time, we identify the Web map processing that should remain in the JavaScript domain as a standardized DOM API. By building the core behaviour of maps and layers into HTML, Web authors who want to build simple maps into their pages can easily do so, supported by core platform technologies, with the power of JavaScript available to enhance the core map and layer behaviour.

By lowering the barriers for Web map authors in this way, we will improve the usability, and standardize the accessibility of Web maps.  Through making map creation a matter of applying appropriately crafted Web platform standards, we will create the conditions to multiply the choices of mapping services offered to authors and users of the Web. 

In improving the choices among mapping services available through the Web platform, we will enable the growth of services that offer alternate means of paying for maps other than in exchange for the user’s personal private information, and we will enable standardized Web map accessibility through addition of maps to HTML. Finally, by making it cheaper to create Web maps than to build mobile apps, we will improve the business rationale for choosing the mobile Web as a development platform, and in doing so we hope the (mobile) Web will benefit from increased ‘success’, or network effects.

<h3 id="goals">Goals</h3>




*   Define the means to allow authors to create dynamic, usable and accessible Web maps about as easily as they can embed an image, a video or a podcast today.
*   Define and embed accessibility of map feature and location information into HTML for use by screen readers and other assistive technology.
*   Define and design security of map information considerations into the Web platform.
*   Define the markup to create mapping mashups that doesn’t necessarily require scripting or detailed mapping server technology knowledge i.e. that can be accomplished about as easily as linking to a document.
*   Simplify the use of public spatial data infrastructures (SDI), such as OpenStreetMap and national and international SDIs, by designing the integration of those services into the proposed Web platform mapping standards.
*   Defining and (advocate for) adding map-enabled HTML to the serialization formats available from existing spatial (map) content management systems, APIs and Web Services.

<h3 id="non-goals">Non-goals</h3>




*   Interoperability with the operating model or availability of existing spatial (map) content management systems, APIs and Web Services. For example, the evolving <a href="https://ogcapi.ogc.org/"><abbr title="Open Geospatial Consortium">OGC</abbr> API</a> standards.

<h3 id="a-high-level-api">A High-Level API</h3>

The <a href="https://extensiblewebmanifesto.org/">Extensible Web Manifesto</a> calls for iterative development and evolution of platform features, starting with low-level ‘primitives’ and resulting eventually in high-level features.  Although there are several low-level primitive proposals inherent or implicated in this proposal, overall this can be seen as a proposal for a high-level feature.  That feature is declarative dynamic Web maps in HTML.  Web mapping is a mature category of JavaScript library that is well into the stage of its development life cycle that some of the aggregate characteristics of those libraries should be incorporated into the platform.  As such, this proposal captures some of the ‘cow paths’ of open and closed source JavaScript Web mapping libraries, as well as taking into consideration how to incorporate server-side mapping services and APIs.

The proposed extension would create a standard [`<map>`](high-level-api.md#the-map-element) widget that contains controls in a user agent shadow root, (similar to `<video>` today), with child [`<layer>`](high-level-api.md#the-layer-element) elements which are in, and may contain, light DOM map-related markup, the vocabulary of which is also part of this proposal.

**See the [High-Level API explainer](high-level-api.md) for details on the proposed elements** that may be referred to in this proposal.

<h2 id="key-scenarios">Key scenarios</h2>


<h3 id="tiled-coordinate-reference-systems">Tiled Coordinate Reference Systems</h3>


Perhaps the most important characteristic of Web maps that is essential for standardization, is the definition of the coordinate reference systems and the scale of the display. 



![Tiled coordinate reference systems](images/tiled-coordinate-reference-systems.png "Tiled coordinate reference systems")


<h3 id="linking">Linking</h3>


<h4 id="including-map-layers-in-html-by-reference">Including map layers in HTML by reference</h4>


[Cartography](https://en.wikipedia.org/wiki/Cartography) is a challenging discipline, no matter the media in which it is performed.  The Web medium demands an entirely new kind of cartography, one which makes maps potentially more valuable than ever, by rendering them dynamically pannable and scalable, among other characteristics.  The new cartographers have a large and powerful set of server technologies, commonly known as [Geographic Information Systems](https://en.wikipedia.org/wiki/Geographic_information_system). Coupled with open data, and public server APIs, Web cartographers are able to easily publish their products.

<h5 id="the-text-mapml-mime-type">The <code>text/mapml</code> MIME type</h5>


So, how to make it simple to make Web maps?  In order to allow HTML authors to make use of existing cartographic services on the Web, such as Web Map Services (WMS) and Web Map Tile Services (WMTS) we propose to create a new document and associated MIME type, `text/mapml`, which can be used by HTML authors and map services to support the key use case of publishing a Web map, with about the same authoring complexity as that of publishing a `<video>`, a `<audio>` or an `<img>`, that is, by using the `<layer src="https://example.org/map/document/url/"></layer>` [formulation](high-level-api.md#bookmark1). 

The proposed doctype for such a map document is  `<!doctype mapml>`, which signals to the HTML parser that it is parsing a MapML document. The root element of a MapML document is `<mapml>`. Like HTML, a MapML document has a `<head>` element, for map document metadata, and a `<body>` for map content.  The content of the document is MapML, that is, the elements from this proposal, which are rendered via coordinates in the coordinate system designated by the `<map>` (`projection` attribute).

<h5 id="links-from-html-to-mapml">Links from HTML to MapML</h5>


It’s one thing to have an element like `<layer>` to provide the client logic for a MapML document embedded in an HTML document, but what should happen if a simple `<a href="">` pointed to a MapML document?  We’ve said that a MapML document should be parseable with the HTML parser, so what should the default behaviour of activating such link be?  Probably, it should be similar to that behaviour which happens when you create a link to a media type that is natively supported by HTML, such as image/png or video/mp4: the browser synthesizes a simple HTML document with a client element such as `<img src="">` or `<video><source src=""></video>`, i.e. it should synthesize an HTML document with a `<map><layer src=""></layer></map>` element with default or generated metadata and parameters.

<h4 id="links-for-layer-alternate-styles">Links for layer alternate styles</h4>



```html
<mapml lang="en">
   <head>
     <meta charset="utf-8">
     <title>States</title>
     <link href="./?style=pophatch" rel="style" title="pophatch">
     <link href="./?style=polygon" rel="style" title="polygon">
     <link href="./?style=" rel="self style" title="population">
   </head>
…
</mapml>
```


Results in a rendered map widget UI with links to mutually exclusive options:

![Named layer styles](images/states-population-style.png "Named layer styles")


Selecting the ‘pophatch’ link responds with a different MapML document:


```html
<mapml lang="en">
   <head>
     <meta charset="utf-8">
     <title>States</title>
     <link href="./?style=pophatch" rel="self style" title="pophatch">
     <link href="./?style=polygon" rel="style" title="polygon">
     <link href="./?style=" rel="style" title="population">
   </head>
…
</mapml>
```

which replaces that layer’s browsing context with a different representation of the layer:


![Alternate layer style](images/states-pophatch-style.png "Alternate layer style")

In the above code example, a MapML document provides links to alternate ‘styles’ of itself.  The current map document is additionally and importantly tagged with the `self` link relation. This link-based facility is very powerful, because what constitutes a ‘style’ for the current map document is in the eye of the document author, and is not limited to a representation with an alternate stylesheet applied to the same data. What is linked to as a ‘style’ may be a radically different rendering of ‘this layer’, even different data, for example a satellite view vs. a topographic/street map rendering (which are obviously not simply different stylesheet renderings of the same data, as in the above example, but are in fact different data for the same extent). 

<h4 id="links-for-alternate-layer-coordinate-systems-projections">Links for alternate layer coordinate systems (projections)</h4>


Geographic Information Systems allow us to provide the same data in a variety of coordinate systems.  When the same layer data is available in different MapML coordinate systems, service providers can make it easy to select the right URL for a layer by encoding links to the current data represented in alternate coordinate systems, in the document metadata (in the head). The user agent can, based on the required projection designated by the `<map projection="">` attribute, automatically select (negotiate) from among the advertised links, as shown below.  The coordinate system selection/negotiation process is transparent and requires no input from the user; if the URL used by the author in the `<layer src="">` value leads to a MapML document with a coordinate system that does not match its parent `<map projection="">` value, the user agent will select the correct advertised alternate to replace it.


```html
<mapml lang="en">
   <head>
       <meta charset="utf-8">
       <title>States</title>
       <meta name="projection" content="OSMTILE">
       <link href="./CBMTILE" rel="alternate" projection="CBMTILE">
       <link href="./APSTILE" rel="alternate" projection="APSTILE">
       <link href="./WGS84" rel="alternate" projection="WGS84">
   </head>
...
</mapml>
```


`alternate` [link relations](https://html.spec.whatwg.org/multipage/links.html#linkTypes) are used to provide alternate representations by format and other criteria.  In this case, the `projection` attribute is used by the UA to distinguish the links within the link group identified by the `alternate` link relation.

<h4 id="links-between-map-services">Links between map services</h4>


This feature could enable federation of map services in a way similar to the ‘federation’ of the HTML Web today, in that in today’s Web, authors decide to link to others’ Web sites and so the federation of sites constituting the Web is enabled.  The MapML-enabled Web should follow a similar path, in that not every Web map needs or can contain all the map information in the world.  So, links should be used to allow map authors to link to others maps in a similar way.  I believe this blends with or is the same as the [links between locations](#links-between-locations) use case, below.

In the `<feature>` markup [shown in the High-Level API explainer](high-level-api.md#bookmark2), a standard hyperlink is shown, wrapped around the outer polygon coordinates of a feature’s geometry:


```html
<polygon>
  <a href="https://example.org/canada/mapml">
    <coordinates class="f72 outer">59.5303058 74.7915903 59.7266984 74.7479857 … 76.1838491 <span class="noline">67.5 76.1894504 67.5 76.7412725 67.5 77.0079535</span> 67.2115912 76.9522523 … 74.727385 59.5303058 74.7915903</coordinates>
  </a>
</polygon>
```


Links like this could have a default visual and accessible cue to indicate link-ness, like its text-wrapping `<a href>` counterpart, that would signal what the user should expect to happen if activated with a gesture.  Useful actions could include: replacing the entire (root) browsing context with another map or HTML document, or loading the link target into the map layer browsing context from which it was activated.

Like `<span>` elements, `<a>` elements could appear within the `<coordinates>` element, allowing links from geometry parts.

<h4 id="links-between-locations">Links between locations</h4>


Links between locations could be marked up similarly to [links between services](#links-between-map-services), possibly with the addition of attributes indicating the location and zoom of the link destination. The current location of a map could be modified by activating links from one location to another.  There might need to be a different visual / accessible cue for such links.  By default, the map might animate in a “fly to” manner in response to link activation.

<h3 id="graceful-degradation-and-progressive-enhancement">Graceful Degradation and Progressive Enhancement</h3>


There are many older browsers still in use on the Web, and they will likely be in use for many years to come, for a variety of reasons.  Fortunately, “client-side image maps” were very popular on the Web at one stage, and this functionality is well supported by older browsers.  For HTML authors who wish to provide a Web mapping experience for users of these older browsers, it should be possible to provide “fallback” markup that enables the core map experience they wish users to have, while providing a progressively enhanced experience for users of evergreen browsers, without relying on archaic scripting APIs.

If `<area>` elements are present (for fallback) as child elements of `<map>`, they are (progressively, if the conditions warrant) treated as `<layer>` elements containing a single geographic feature, with coordinates in the `coords` attribute being interpreted as being valid pixel coordinates in the map’s locally defined map coordinate system.   More detail and a working example of [how graceful degradation and progressive enhancement could work](https://maps4html.org/Web-Map-Custom-Element/blog/progressive-web-maps.html) in this proposal is available.

<h3 id="polyfill">Polyfill</h3>

A polyfill for the [High-Level API](high-level-api.md) is available.

- A [custom `<map>` element prototype](https://github.com/Maps4HTML/Web-Map-Custom-Element/blob/master/index-web-map.html) is available with some caveats; it’s not yet a fully compliant ‘polyfill’. The prototype [doesn’t work in WebKit](https://caniuse.com/#feat=mdn-api_customelementregistry_builtin) due to the use of unsupported custom built-in elements. And unfortunately, `<map>` _as a custom element_ has a [severe accessibility issue](https://github.com/w3c/html-aam/issues/292) due to the nature of current implementations in some browsers.
- A parallel [`<mapml-viewer>`](https://github.com/Maps4HTML/Web-Map-Custom-Element/blob/master/index-mapml-viewer.html) autonomous custom element suite is available in all major browsers. A [demo](https://geogratis.gc.ca/mapml/) is available.

The light DOM content of `<layer>` is not currently active or available as an API.

<h2 id="detailed-design-discussion">Detailed design discussion</h2>


<h3 id="use-cases-and-requirements">Use Cases and Requirements</h3>


This proposal is being [evaluated](https://github.com/Maps4HTML/MapML/labels/Requirements%20Evaluation)
against the [Use Cases and Requirements for Standardizing Web Maps](https://maps4html.org/HTML-Map-Element-UseCases-Requirements/),
to identify gaps between the required functionality and the polyfilled behaviour.

See the [MapML Use Case, Requirement, and Capability Matrix](https://maps4html.org/UCR-MapML-Matrix/mapml-ucrs-fulfillment-matrix.html)
for how MapML compares in capabilities in contrast to existing popular web mapping libraries.

<h2 id="considered-alternative-designs-of-mapml">Considered alternative designs of MapML</h2>


TBD - we have considered many alternatives, I have just run out of steam to document them, at the moment. Also this document is already quite long.  As things progress, I will add content here.

<h3 id="alternative-approaches">Alternative Approaches</h3>

*   [SVGMap](https://discourse.wicg.io/t/vector-tiling-on-svgmap/3135) - is it possible to merge the SVGMap proposal and this proposal? Or are they competing proposals?
*   APIs: [Leaflet](https://leafletjs.com/), [OpenLayers](https://openlayers.org/) and others, (albeit others without any notion of cross-origin resource sharing) provide excellent map scripting APIs and events.  Can these or similar APIs be built on top of the proposed HTML infrastructure? Would life be simpler for authors with the proposed HTML?
*   Status quo

<h2 id="stakeholder-feedback-opposition">Stakeholder Feedback / Opposition</h2>


Some participants have said we should start over, because of the [sunk costs fallacy](https://en.m.wikipedia.org/wiki/Sunk_cost#Concorde_effect); that doesn’t seem to be in the spirit of iteration, nor hopefully is it correct to see this proposal as a waste of energy or money.  A better strategy would be to solicit concrete, actionable and incremental change requests.  It is in that spirit that the current explainer is offered. 

The objective of this project is to get Web browser projects to agree that Web maps as a high level feature are desirable and that the proposal is implementable, and _then_ to implement and ship the feature.  To get there from here, we need browser developer participation, the absence of which appears equivalent to opposition. So, there is work to do.

<h2 id="references-and-acknowledgements">References and Acknowledgements</h2>


Contributions, advice and support from the following people are gratefully acknowledged:

Benoît Chagnon, Brian Kardell, Michael<sup>tm</sup> Smith, Robert Linder, Joan Maso, Keith Pomakis, Gil Heo, Jérôme St-Louis,  Amelia Bellamy-Royds, Nic Chan, Nick Fitzsimmons, Simon Pieters, Tom Kralidis, Daniel Morissette, Chris Hodgson

If I’ve forgotten to mention you, please open an issue.

Errors and omissions are certainly my own; if you spot a correction needed in the above, please open an issue. 
