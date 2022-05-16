<h1>The MapML (Map Markup Language) explainer</h1>

Specification: https://maps4html.org/MapML/spec/

<h2>Author</h2>

Peter Rushforth

<h2>Participate</h2>

The W3C [Maps for HTML Community Group](https://www.w3.org/community/maps4html/) 
is iterating on the problem space. 
You can contribute to the on-going discussion and documentation of 
[Use Cases and Requirements for Standardizing Web Maps](https://maps4html.org/HTML-Map-Element-UseCases-Requirements/). 
Alternatively, if your organization is a member of the 
[Web Platform Incubator Community Group](https://www.w3.org/community/WICG/) (WICG) 
and you are able to contribute there but not elsewhere, 
please consider contributing through the [WICG forum on Web mapping](https://discourse.wicg.io/c/web-mapping/22). 
We would love to hear from you.

[Issue tracker for this explainer](https://github.com/Maps4HTML/MapML-Proposal/issues)

<h2 id="introduction">Introduction</h2>

Web maps are a well-established class of Web application, and there exist popular, 
mature open- and closed-source JavaScript frameworks to create and manage Web maps.  
Web maps, considered from the perspective of the Extensible Web Manifesto, represent 
a “high-level API”.  The many low-level browser APIs already used by the popular 
JavaScript mapping frameworks demonstrate that Web maps may be a good fit for 
inclusion into the Web platform standards at this time.  

MapML is the declarative API that results from our community’s effort to abstract 
and polyfill Web mapping requirements as custom elements, using one of the mature 
open source Web mapping frameworks so as to not have to start “from scratch”.  
The process of polyfilling Web maps as MapML has also identified missing low-level 
platform APIs that will be essential to complete the implementation of Web maps 
as a standard platform feature.

<h2>Contents</h2>

- [The Problem](#the-problem)
- [The Proposal](#the-proposal)
- [Goals](#goals)
- [Use Cases and Requirements](#use-cases-and-requirements)
- [Key Scenarios](#key-scenarios)
- [Detailed design discussion](#detailed-design-discussion)
- [Considered Alternative Designs of MapML](#considered-alternative-designs-of-mapml)
- [Stakeholder Feedback / Opposition](#stakeholder-feedback--opposition)
- [References and Acknowledgements](#references-and-acknowledgements)

<h2 id="the-problem">The Problem</h2>

Web maps are <span style="text-decoration:underline;">not interoperable</span>. 
For example, users cannot “mash up” a Leaflet map and an OpenLayers map, two of 
the most popular open source Web mapping frameworks, even if they show the same 
exact data source in the exact same location.  (The situation is even more 
non-interoperable between open map data and privately managed map databases, 
where the sole client may be that provided by the owner of the map data. The 
lack of legal interoperability of map data is outside the scope of standardization.)

The lack of Web map interoperability affects developers. The skill set and 
vocabulary required to create a robust map is different from one mapping framework 
to the next. They can be complex to <span style="text-decoration:underline;">create</span>, 
with visual styling of map information being a discipline that is generally 
outside the realm of Web technology, and different from one mapping framework to 
the next.

Web maps are [typically not accessible](https://github.com/Malvoz/web-maps-wcag-evaluation/blob/master/README.md). 
The knowledge required to <span style="text-decoration:underline;">use</span> Web 
maps is different for each framework, with resulting accessibility challenges 
arising from inconsistencies in user experience across frameworks.

Location information is <span style="text-decoration:underline;">not searchable</span>, 
except through specialized portals. Web maps are not crawlable; even if the JavaScript 
that creates a Web map is executed by a crawler, the resulting DOM requires a 
sighted user to interpret it, because it only has visual semantics.  The DOM created 
also varies from framework to framework. As a result, users cannot search for 
location information, embedded as it is in program code, except in specialized 
mapping portals. Location search may be even more important for non-visual users 
especially in a mobile context, but it is impossible because of today’s Web mapping 
frameworks.

<h2 id="the-proposal">The Proposal</h2>

The proposal is to create accessible, searchable and usable Web maps, by integrating 
mapping elements into HTML supported by the CSS and DOM standards.

This extension to HTML would create a `&lt;map>/&lt;mapmlviewer>*` viewer widget 
that contains default controls in a user agent shadow root, (similar to `&lt;video>` 
today), with child `&lt;layer>` elements which are light DOM, which may either 
refer to remote map source or contain map-related markup (the vocabulary of which 
is also part of this proposal):

```html
<mapmlviewer zoom="11" lat="48.8566" lon="2.3522" controls controlslist="nolayer noreload" projection="OSMTILE">
  <layer src="https://example.com/mapml/osm/" checked crossorigin></layer>
  <!-- more layers as required -->
</mapmlviewer>
```

Please review  the [user documentation](https://maps4html.org/web-map-doc/docs/) 
for the proposed declarative syntax and semantics, with examples.

\* The name of the eventual root map widget element will be determined in consultation 
with browser teams. “&lt;map>” is used by an existing element, and it may be 
impossible or undesirable to overload the behavior of that element.

<h3 id="goals">Goals</h3>

* Allow authors to create dynamic, usable and accessible Web maps about as easily 
as they can embed an image, a video or a podcast today, using markup, especially links.
* Allow authors to create mapping mashups using markup. Use of markup doesn’t 
necessarily require scripting development skills, or detailed map server technology 
knowledge i.e. a mashup can be accomplished about as easily as linking to images 
or videos..
* Embed semantics of map feature and location information into HTML and browsers 
for use by screen readers and other assistive technologies.
* Simplify the use of public or private [spatial data infrastructures](https://en.wikipedia.org/wiki/Spatial_data_infrastructure) 
(SDI), such as [OpenStreetMap](https://wiki.openstreetmap.org/wiki/Main_Page) 
and national and international SDIs, by designing the integration of those services 
into the proposed Web platform mapping standards.
* Leverage existing spatial (map) content management systems, APIs and Web Services, 
so that map content is immediately available.

<h3 id="use-cases-and-requirements">Use Cases and Requirements</h3>

This proposal presents the [Use Cases and Requirements for Standardizing Web Maps](https://maps4html.org/HTML-Map-Element-UseCases-Requirements/) 
and our polyfill for review by the W3C community.

<h3 id="key-scenarios">Key Scenarios</h3>

See the Use Cases document for a comprehensive list of scenarios.  A few of the more important scenarios include:

* Simple, accessible, user-controllable mashups
* Allow layers to be turned on/off by user
* Accessible location information, styled using CSS
* Accessible pan and zoom
* Show the user’s location on a map
* Web search for location information
* Accessible display of routes between locations
* Leverage existing geospatial content
* APIs to support more performant, complex mapping applications

<h3 id="detailed-design-discussion">Detailed design discussion</h3>

_See the [High-Level API explainer](https://github.com/Maps4HTML/MapML-Proposal/blob/main/high-level-api.md) for design details on proposed elements._

_See the polyfill [user documentation](https://maps4html.org/web-map-doc/docs/elements/mapml-viewer/)._


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

<h2 id="stakeholder-feedback-opposition">Stakeholder Feedback / Opposition</h2>

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
of the pages included in the HTTP Archive.

Bocoup also received the feedback from browser developers that support from map 
framework developers would be important in any browser team’s decision to implement 
the proposal.  Browser developers also told interviewers that a polyfill 
implementation would make it possible for browser teams and others to readily 
evaluate the proposal.

<h2 id="references-and-acknowledgements">References and Acknowledgements</h2>

Contributions, advice and support from the following people are gratefully acknowledged:

Robert Linder, Simon Pieters, Anssi Kostiainen, Amelia Bellamy-Royds, Doug Schepers,  
Brian Kardell, Boaz Sender, Ahmad Ayubi, Anshpreet Sandhu, Ben Lu, Aliyan Haq, 
Michael<sup>tm</sup> Smith, Benoît Chagnon, Joan Masó, Keith Pomakis, Gil Heo, 
Jérôme St-Louis, Nic Chan, Nick Fitzsimmons, Tom Kralidis, Daniel Morissette, 
Chris Hodgson, Bennett Feely
