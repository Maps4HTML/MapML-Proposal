<h1>The MapML (Map Markup Language) explainer</h1>

Draft spec: https://maps4html.org/MapML/spec/

<h2>Author(s)</h2>

Peter Rushforth, Natural Resources Canada
Aliyan Haq, Natural Resources Canada

<h2>Participate</h2>

The W3C [Maps for HTML Community Group](https://www.w3.org/community/maps4html/) 
is iterating on the problem space. 
You can contribute to the on-going discussion and documentation of 
[Use Cases and Requirements for Standardizing Web Maps](https://maps4html.org/HTML-Map-Element-UseCases-Requirements/). 

[Issue tracker for this explainer](https://github.com/Maps4HTML/MapML-Proposal/issues)

<h2 id="introduction">Introduction</h2>

We propose to extend the Web platform with HTML primitives that implement map and 
location semantics.  We call the proposed HTML extension "MapML".

The core idea is to easily enable web developers to create map viewports on a web 
page that are georeferenced, and into which HTML map content may be added in 
declarative fashion, and manipulated or accessed using the DOM. 

The map viewport is a rectangular window in a web page in which "real-world" 
coordinate reference systems are used, and viewport-to-real-world scales may be 
used for positioning and styling of HTML map content. The geometric or mathematical 
relationship between the viewport and the "real world" is provided and managed by 
the browser, especially by the browser rendering engine.

MapML Polyfill

We have developed a polyfill for MapML, using custom elements,
in which we've tried to follow platform advice.  The user documentation for the 
polyfill is [available online here](https://maps4html.org/web-map-doc/).

In some cases, existing HTML elements have a required common function, so we have 
proposed to extend those elements with new attributes, properties, events and 
behavior.  

In other cases, there is a need for an entirely new element or element group, 
e.g. <map-extent> 

The polyfill is implemented especially using autonomous custom elements.  
Many of our polyfill's custom elements use the 'map-' namespace prefix.  In all 
cases where we propose to extend a current native HTML element, we have created 
an autonomous custom element named 'map-'whatever e.g. <map-link>  We were forced 
to do this because of the lack of interoperability of customized built-in elements.

<h2>Contents</h2>

- [Introduction](#introduction)
- [Motivation](#motivation)
- [The Proposal](#the-proposal)
- [Goals](#goals)
- [Use Cases and Requirements](#use-cases-and-requirements)
- [Key Scenarios](#key-scenarios)
- [Considered Alternative Designs of MapML](#considered-alternative-designs-of-mapml)
- [Stakeholder Feedback](#stakeholder-feedback)
- [References and Acknowledgements](#references-and-acknowledgements)

<h2 id="motivation">Motivation</h2>

Web maps are a widespread and important, not to say essential, feature of the modern 
web.  Maps occur on 16% of web pages, and 22% of web maps contain interactive 
representations of places (aka "features"). The number of maps on web pages exceeds 
that of other media types, including \<video> and YouTube embeds [1](#references-and-acknowledgements).

Web maps are <span style="text-decoration:underline;">not interoperable</span>.  
Maps from one provider cannot be readily combined with maps from another provider, 
and even when such mashups are technically feasible due to adherence of the provider 
to server interoperability standards, such as the widely implemented OGC web 
service standards, barriers on the client will often prevent information re-use.  

In many cases, client barriers exist because the map information is represented 
as (JavaScript) code, providing a noteworthy exemplar of the rationale behind the Rule 
of Least Power [2](#references-and-acknowledgements). For example, users cannot 
“mash up” a Leaflet map and an OpenLayers map, two of the most popular open source 
Web mapping frameworks, even if they show the exact same data source in the exact 
same location.  

The situation becomes even less interoperable between open map data and privately 
managed map databases, where the sole client (also in JavaScript) may be that provided 
by the owner of the map data. Although the lack of legal interoperability of map 
data is outside the scope of standardization, it should be technically feasible 
to create a client Web map interoperability standard that lowers or eliminates 
the technical barriers for map developers who wish to have and to provide choices to 
their users based on standards. If a web standard for maps existed, it might reduce 
the financial incentives to create proprietary barriers to map re-use in the same 
way that publishing textual information on the Web has reduced the incentive to 
create proprietary document management and publishing technology.

The representation of maps-as-code also leads to a problem of resource discovery. 
The location of information is often relevant for search results, demonstrated by 
the fact that many web search engines expose (virtual) map tab in which to visualize 
search results on a map.  If the location of web page information was organically 
exposed as declarative standard markup (and not as code), it would (in principle) 
be possible for any web crawler to create location-enabled full text indexes of 
the web, which might reasonably be useful for search relevance ranking and improved, 
or at least, more open resource discovery.

The lack of Web map interoperability also affects web developers. The skill set 
required to create a robust map is different from one mapping framework to the 
next, especially with regard to feature styling. Maps can be complex to 
<span style="text-decoration:underline;">create</span>, with visual styling of map 
information being a discipline that today is generally outside the scope of Web 
technology (CSS can't be applied), and is different from one mapping framework 
to the next.  Ideally, it would be possible for developers to apply their existing 
knowledge of CSS, HTML and SVG to maps and location information problems. 

Web maps are [often inaccessible](https://github.com/Malvoz/web-maps-wcag-evaluation/blob/master/README.md). 
For users, the skills required to <span style="text-decoration:underline;">use</span> Web 
maps is different for each framework, with resulting accessibility challenges 
arising from inconsistencies in user experience across frameworks.  Users have
no standardized ability to express accessibility preferences that relate to
map content or controls.

<h2 id="the-proposal">The Proposal</h2>

Please review  the [user documentation](https://maps4html.org/web-map-doc/docs/) 
for the polyfill declarative syntax and semantics, with examples.  

This extension to HTML would create a map viewer widget similar to `&lt;video>`, 
with child `&lt;layer->` elements in light DOM. Layers either 
refer to remote map data source or contain map-related markup inline / in the 
element's content:  

```html
<mapml-viewer zoom="11" lat="48.8566" lon="2.3522" controls controlslist="noreload geolocation" projection="OSMTILE">
  <!-- remote content layer -->
  <!-- xhtml syntax and namespace only (currently) for remote content, root element='mapml-' (no doctype yet) -->
  <layer- src="https://example.com/mapml/openstreetmap/tiles" checked hidden crossorigin></layer->
  <!-- local content layer / content 'inline' -->
  <layer- checked label="Places of Interest">
    <!-- html or xhtml syntax for inline content -->
    <map-extent projection="OSMTILE" checked hidden>
      <map-input type=zoom min=0 max=18>
      <map-input type=location name=xmin position=top-left min=... max=... axis=easting></map-input>
      <map-input type=location name=ymax position=top-left min=... max=... axis=northing></map-input>
      <map-input type=location name=xmax position=bottom-right min=... max=... axis=easting></map-input>
      <map-input type=location name=ymin position=bottom-right min=... max=... axis=northing></map-input>
      <map-input type=width name=w min=1 max=4096></map-input>
      <map-input type=height name=h min=1 max=4096></map-input>
      <map-link rel=features media=... tref=/geoserver/wms/?request=GetMap&bbox={xmin},{ymin},{xmax},{ymax}&width={w}&height={h}&format=text/mapml&format_options=mapmlfeatures:true...></map-link>
    </map-extent>
  </layer->
  <!-- more / different layers as required, add or remove via DOM node interface -->
</mapml-viewer>
```

<h3 id="goals">Goals</h3>

* Allow authors to create dynamic, usable and accessible Web maps about as easily 
as they can embed an image, a video or a podcast today, using markup, especially links.
* Allow authors to create accessible mapping mashups using markup. Use of markup doesn’t 
necessarily require scripting development skills, or detailed map server technology 
knowledge i.e. a mashup can be accomplished about as easily as linking to images 
or videos..
* Define semantics of map feature and location information in HTML and browsers 
for use by screen readers and other assistive technologies.
* Simplify the use of public or private [spatial data infrastructures](https://en.wikipedia.org/wiki/Spatial_data_infrastructure) 
(SDI), such as [OpenStreetMap](https://wiki.openstreetmap.org/wiki/Main_Page) 
and national and international SDIs, by designing the integration of those services 
into the proposed Web platform mapping standards.
* Leverage existing spatial (map) content management systems, APIs and Web Services, 
so that map content is immediately available when this proposal is implemented.

<h3 id="use-cases-and-requirements">Use Cases and Requirements</h3>

This proposal presents the [Use Cases and Requirements for Standardizing Web Maps](https://maps4html.org/HTML-Map-Element-UseCases-Requirements/) 
and our polyfill for review by the W3C community.

<h3 id="key-scenarios">Key Scenarios</h3>

See the Use Cases document for a comprehensive list of scenarios.  A few of the 
more important scenarios include:

* Simple, accessible, user-controllable mashups
* Allow layers to be turned on/off by keyboard / mouse user
* Accessible location information, styled using CSS
* Accessible / meaningful pan and zoom
* Allow user to register map content preferences via user agent settings
* Show the user’s location on a map, and / or use map bounds for responsive content display
* Web search for location information
* Accessible display of routes between locations
* Leverage existing geospatial content
* APIs to support more performant, complex mapping applications

<h2 id="considered-alternative-designs-of-mapml">Considered alternative designs of MapML</h2>

Elements of MapML could be improved through discussion and further research, 
especially by establishing an on-going collaboration between Web map framework 
developers and Web platform experts and standards developers.  The Maps for HTML 
Community Group believes that the specifications and polyfills established so far 
form a reasonable starting point for further collaboration.  

Such collaboration is essential to the success of the project, because of the 
cross-disciplinary nature of the subject. On one hand, Web map framework developers 
have the required detailed understanding of the spatial domain, including the 
concepts of coordinate reference systems, spatial content management APIs, such 
as OGC WMS, WMTS, WFS, as well as a working knowledge of Web technology and standards. 
On the other hand, Web platform experts and standards developers possess the 
understanding of not only Web architecture, technology and standards, but also Web 
accessibility.  To succeed, this project requires a narrow collaboration between 
these stakeholder groups.  

<h2 id="stakeholder-feedback">Stakeholder Feedback</h2>

Natural Resources Canada hosted the 2020 
[W3C/OGC Joint Workshop Series on Maps for the Web](https://www.w3.org/2020/maps/) 
in cooperation with the Maps for HTML Community Group.

The presentations and [conclusions](https://www.w3.org/2020/maps/report) of the 
workshop participants were mostly positive, especially towards accessible Web map 
standardization.  The most significant concerns raised were around Web gatekeeping, 
and funding.

In 2022, [Bocoup](https://www.w3.org/community/maps4html/participants?search=bocoup) 
conducted HTTP Archive and Web developer research on the subject of Web platform maps, 
which revealed that there is strong support among developers for simple, accessible 
Web platform maps.  Their research also revealed that maps exist on about **16%** 
of the pages included in the HTTP Archive [1].

Bocoup also received the feedback from browser developers that support from map 
framework developers would be important in any browser team’s decision to implement 
the proposal.  Browser developers also told interviewers that a polyfill 
implementation would make it possible for browser teams and others to readily 
evaluate the proposal.

<h2 id="references-and-acknowledgements">References and Acknowledgements</h2>

[1] [Web Maps Research Summary](https://docs.google.com/document/d/e/2PACX-1vRXTxEOqP6xmdgPEmqir8r-kDvwVfA8oTC4vvv_XhoRk9mClLtzMx0BdoMaPIctwftkWI3U7yTS3Bkq/pub)
[2] [The Rule of Least Power](https://www.w3.org/2001/tag/doc/leastPower.html)
 

Contributions, advice and support from the following people are gratefully acknowledged:

Robert Linder, Simon Pieters, Anssi Kostiainen, Amelia Bellamy-Royds, Doug Schepers,  
Brian Kardell, Boaz Sender, Ahmad Ayubi, Anshpreet Sandhu, Ben Lu, Aliyan Haq, 
Michael<sup>tm</sup> Smith, Benoît Chagnon, Joan Masó, Keith Pomakis, Gil Heo, 
Jérôme St-Louis, Nic Chan, Nick Fitzsimmons, Tom Kralidis, Daniel Morissette, 
Chris Hodgson, Bennett Feely, the GeoServer project
