<h1>The MapML (Map Markup Language) explainer</h1>

Specification: https://maps4html.org/MapML/spec/

<h2>Author</h2>

Peter Rushforth

<h2>Participate</h2>

The W3C [Maps for HTML Community Group](https://www.w3.org/community/maps4html/)
is iterating on the problem space.
You can contribute to the on-going discussion and documentation of
[Use Cases and Requirements for Standardizing Web Maps](https://maps4html.org/HTML-Map-Element-UseCases-Requirements/).

**Discussion venues:**
- [Maps4HTML GitHub Organization](https://github.com/Maps4HTML/) - specifications, proposals, and discussions
- [Issue tracker for this explainer](https://github.com/Maps4HTML/MapML-Proposal/issues)
- [Public mailing list](mailto:public-maps4html@w3.org) - public-maps4html@w3.org ([archives](https://lists.w3.org/Archives/Public/public-maps4html/))

<h2 id="introduction">Introduction</h2>

**MapML (Map Markup Language) is a proposal to make maps and location a first-class feature of HTML**, similar to how `<img>`, `<video>`, and `<audio>` brought media natively to the web. Just as you can embed a video with a simple `<video src="...">` tag, MapML would let you embed an interactive map with `<mapml-viewer>` and declarative markup—no JavaScript libraries required for basic use cases.

The web platform has progressively added support for text, images, video and audio, but not yet for location and maps. This gap forces developers to rely on complex JavaScript libraries and pushes location-based information into proprietary, centralized services. Users face inconsistent accessibility, privacy concerns from tracking-based mapping services, and a mobile web experience that lags behind native apps—**especially for maps and location features**. MapML addresses these problems by bringing maps into the browser as a web-native feature, making location data linkable, crawlable, and accessible like other web content.

**Current state:** The [W3C Maps for HTML Community Group](https://www.w3.org/community/maps4html/) is developing MapML through an iterative, collaborative process. A working polyfill implementation (built on Leaflet and Proj4) is [available via npm](https://www.npmjs.com/package/@maps4html/mapml/v/latest), and a [MapML extension for GeoServer](https://docs.geoserver.org/latest/en/user/extensions/mapml/index.html) demonstrates how existing geospatial services can adopt the format. This explainer presents the proposal for broader web platform consideration. MapML was the subject of a question in the State of HTML 2025 survey.

<h2>Contents</h2>

- [The Problem](#the-problem)
  - [User-Facing Problem](#user-facing-problem)
  - [Direct User Impact](#direct-user-impact)
  - [Underlying Causes](#underlying-causes)
  - [The Standards Gap](#the-standards-gap)
- [The Proposal](#the-proposal)
  - [How It Works](#how-it-works)
  - [Benefits](#benefits)
  - [Design Philosophy: Progressive Enhancement at the Standards Level](#design-philosophy-progressive-enhancement-at-the-standards-level)
  - [Goals](#goals)
  - [Non-Goals](#non-goals)
  - [Example Markup](#example-markup)
- [Internationalization](#internationalization)
- [Accessibility Considerations](#accessibility)
- [Use Cases and Requirements](#use-cases-and-requirements)
- [Alternative Approaches](#alternative-approaches)
  - [SVGMap, Hyper-Layering Architecture (HLA) and Layers as Web Apps (LaWAs)](#svgmap-hyper-layering-architecture-hla-and-layers-as-web-apps-lawas)
  - [JavaScript Mapping Libraries](#javascript-mapping-libraries)
  - [Status Quo (No Standardization)](#status-quo-no-standardization)
- [Stakeholder Feedback / Opposition](#stakeholder-feedback--opposition)
- [References and Acknowledgements](#references-and-acknowledgements)


<h2 id="the-problem">The Problem</h2>

## User-Facing Problem

Location is a fundamental primitive for information on the web—whether searching for businesses, events, services, or news. Yet the web platform does not provide native support for location as a primitive, unlike text, images, or video.

**Maps are widely used but poorly supported:** Research by Bocoup analyzing the HTTP Archive dataset (7.9 million web pages) found that [approximately 16% of web pages use maps](https://www.bocoup.com/blog/no-neutral-map)—significantly more than `<video>` (4.1%) or `<picture>` (5.9%) elements. Of those pages with maps, 20-26% use complex geographic features like markers, popups, and polygons. Despite this widespread use, there is no native browser support for maps, forcing developers to rely entirely on JavaScript libraries.

### Direct User Impact

**Accessibility barriers:**  
People using screen readers face significant barriers navigating maps and understanding spatial relationships. Current web maps built with JavaScript libraries have [highly variable accessibility](https://github.com/Malvoz/web-maps-wcag-evaluation/blob/master/README.md), creating an inconsistent and often exclusionary experience. In Bocoup's survey of web developers, 84% reported they don't have the necessary tools to make accessibility-compliant maps.

**Privacy and surveillance:**  
Accessing location-based information typically requires using centralized services that typically track users' locations and search interests, often for targeted advertising. Users have few privacy-preserving alternatives.

**Inconsistent experience:**  
Map interfaces vary widely across sites, and mobile web maps often don't work as well as native apps, pushing users off the open web.

### Underlying Causes

These user problems stem from deeper structural issues:

**Location data is not web-native:**  
Unlike text or images on the web, location information cannot be natively linked, crawled, or indexed by search engines. Web pages with location data cannot easily become "features" on a map or participate in spatial search.

**Barriers to decentralization:**  
Because the web lacks native support for location, and because maps and location services are complex and resource-intensive to build, the current landscape encourages centralization. Large platforms are typically required, and they typically control both the mapping databases and the search infrastructure, creating barriers to entry for smaller providers. Search engines cannot easily index the web's location data because it's locked in proprietary domains, behind inaccessible JavaScript.

**High complexity for authors:**  
Creating accessible, usable, and privacy-preserving web maps requires specialized knowledge, putting it out of reach for most web authors. This high barrier reinforces centralization—only well-resourced platforms can afford to build maps, limiting user choice. Bocoup's research found that approximately half of web developers say there aren't enough beginner-friendly mapping solutions available, with "implementing interactions" cited as the most difficult step when making maps.

### The Standards Gap

The web's architecture requires search to make the web useful—from early link directories to modern search algorithms. However, the **lack of standardization for location and maps in native HTML** has resulted in the centralization of location-based content into major web portals (such as Google Maps). The geographic web effectively exists outside the open web platform, accessible only through proprietary services.

By adding native support for location in HTML (making location data linkable and crawlable), we can:
- Enable distributed providers of location-based search, giving users more choices
- Allow the "map database" to **become** the web itself
- Reduce surveillance by enabling privacy-preserving alternatives
- Lower barriers for authors, increasing the diversity of mapping services available to users

**In summary:**  
The lack of native location support in HTML creates direct user harm (accessibility, privacy, poor mobile experience) and systemic harm (centralization, surveillance, lack of user choice). Implementing location as a first-class web primitive addresses both.

<h2 id="the-proposal">The Proposal</h2>

We propose adding native HTML support for maps and location by introducing **MapML** (Map Markup Language) - a web-native format that makes location a first-class primitive, similar to how `<img>`, `<video>`, and `<audio>` made media first-class web content.

### How It Works

MapML follows the **"pave the cowpaths"** principle. Rather than replacing existing geospatial infrastructure (Web Map Services, Web Map Tile Services, OGC APIs, STAC, etc.), MapML provides a **standardized HTML interface** to these services. Map providers can offer MapML as an additional format via **existing standard APIs**.

**MapML *is* HTML** - it's parsed by the HTML parser and uses the same vocabulary. Adding MapML support to existing map services (WMS, WMTS, etc.) means simply offering HTML as an additional serialization format. This small step by service providers enables massive global geospatial services interoperability in browsers.

**Proven feasibility:** The Maps for HTML Community Group has contributed a [MapML extension module to GeoServer](https://docs.geoserver.org/latest/en/user/extensions/mapml/index.html), a widely-used open source geospatial server platform. This demonstrates that existing map services can adopt MapML without disrupting their current operations, serving as an exemplar for other service providers. MapML is implemented as standard web custom elements using the Leaflet and Proj4 web mapping engines. MapML.js is embedded in the GeoServer extension and is also [available via npm](https://www.npmjs.com/package/@maps4html/mapml/v/latest) for developers to use today.

**Key concept:** MapML embodies the web architecture principle of **"Hypertext as the Engine of Application State" (HATEOAS)**. Just as HTML encodes hyperlinks, images, and videos with their behaviors, MapML encodes maps and locations as they are already essentially implemented and understood on the web - as map images, map features, tiling, vector tiles, coordinate reference systems, map scale, panning, zooming, legends, attribution/metadata, layering, and styling. While this proposal defines a specific set of capabilities, it also establishes a foundation for future collaboration and innovation between the web and geospatial communities.

**Simple example:**

```html
<mapml-viewer zoom="11" lat="48.8566" lon="2.3522" controls>
  <map-caption>Paris, the City of Light</map-caption>
  <map-layer src="https://example.com/mapml/osm.mapml" checked></map-layer>
</mapml-viewer>
```

This declarative syntax works like `<video>` - authors can embed interactive maps without requiring JavaScript expertise.

### Benefits

**For users:**
- **Consistent accessibility** - Screen readers and assistive technology can reliably navigate maps
- **Privacy options and choices** - Enables decentralized, privacy-preserving mapping alternatives
- **Better mobile web** - Native support improves performance and user experience

**For authors:**
- **Lower barriers** - Create accessible maps as easily as embedding an image or video
- **Standards-based** - Use HTML, CSS, and standard DOM APIs
- **Progressive enhancement** - JavaScript and CSS available for advanced features when needed

**For the web ecosystem:**
- **Decentralization** - Lowers barriers for diverse mapping service providers
- **User choice** - More options for users in how they access location-based information
- **Crawlable location data** - Search engines can index spatial information like other web content

### Design Philosophy: Progressive Enhancement at the Standards Level

The MapML proposal follows a two-phase approach to naming and integration:

**Phase 1 - Polyfill (current):** The polyfill uses custom element names like `<mapml-viewer>`, `<map-layer>`, and `<map-caption>` to avoid any collision with existing HTML elements. This conservative approach allows the community to validate the design, gather implementation experience, and demonstrate real-world usage without web compatibility risk.

**Phase 2 - Standards Integration (future):** The path to standardization will favour **extending existing HTML elements with new attributes** where semantically appropriate and web-compatible, rather than proliferating new elements. For example:
- `<link>` could gain map-specific `rel` values (e.g., `rel="map-stylesheet"`) rather than requiring a new `<maplink>` element
- `<input>` could support new map control types (e.g., `type="map-zoom"`) rather than `<map-input>`
- Similarly for other concepts that naturally extend existing element semantics

This approach applies **progressive enhancement to the standard itself**: in non-compliant user agents, the existing element provides fallback behaviour, while compliant browsers enhance it with map-specific capabilities. This strategy minimizes growth of the HTML vocabulary while maintaining graceful degradation.

Where new elements are genuinely needed (concepts with no existing HTML analog), they will be proposed as distinct additions. The final element and attribute design bikeshed will be determined through the standards process based on:
- Web compatibility testing
- Implementer feedback  
- Semantic fit with existing HTML patterns
- Progressive enhancement feasibility

<h3 id="goals">Goals</h3>

- Enable declarative, accessible maps in HTML
- Lower barriers for authors to create web maps
- Make location data crawlable and linkable on the web
- Enable user choice among diverse mapping service providers
- Integrate with existing geospatial infrastructure where feasible

<h3 id="non-goals">Non-Goals</h3>

- **Replacing existing geospatial services** - MapML doesn't aim to replace WMS, WMTS, OGC APIs, STAC, or other geospatial standards, but to provide a web-native interface to them
- **Replacing JavaScript mapping libraries** - MapML provides a declarative layer for common use cases, not a complete replacement for specialized mapping libraries. With native rendering capabilities, libraries could potentially grow in power by leveraging browser-native mapping APIs for styling, visualization and data access
- **3D mapping or globe rendering** - The current focus is on 2D web maps (though 3D could be considered as future work)
- **Complete GIS functionality** - MapML is not intended to replicate the full capabilities of desktop GIS software
- **Generalized pan/zoom for web content** - MapML's pan and zoom functionality is specific to maps and relies on coordinate reference system semantics that are not shared by non-geospatial web content

<h3 id="example-markup">Example Markup</h3>

MapML provides a declarative HTML interface similar to `<video>`. Map viewer elements contain controls in a shadow root (like `<video>` today) with child layer elements in the light DOM:

```HTML
<mapml-viewer zoom="11" lat="48.8566" lon="2.3522" controls controlslist="nolayer noreload">
  <map-layer src="https://example.com/mapml/osm/" checked crossorigin></map-layer>
</mapml-viewer>
```
<p><img src="images/map-example-paris.png" width="300" height="150" alt="Map of Paris"></p>

*See the [MapML Element Reference](https://maps4html.org/web-map-doc/docs/elements/mapml-viewer/) for detailed documentation on elements, attributes, and the polyfill implementation.*

<h2 id="internationalization">Internationalization</h2>

MapML supports internationalization through standard web mechanisms:

**HTML `lang` attribute:** The MapML.js library respects the standard HTML `lang` attribute on ancestor elements of the `<mapml-viewer>`. For example, setting `lang="fr"` on a parent element localizes the map viewer UI to French. MapML.js natively supports English and French localization, developed for Canada's bilingual official language environment.  This is understood to be a non-standard behaviour, but it is useful operationally.

**Browser locale detection:** With the [MapML browser extension](https://maps4html.org/web-map-doc/docs/extension/features#localization) installed in developer mode, MapML.js can detect the user's browser locale using the browser's Internationalization API (which is not directly available to scripts). The extension enables automatic localization to the user's preferred language when locale resources are available. English, French, Ukrainian and Swedish browsers are currently available.

**Community contributions:** Additional locale support can be contributed by the community.

This approach ensures that maps can be accessible to users in their preferred language, following established web internationalization practices.

<h2 id="accessibility">Accessibility Considerations</h2>

MapML aims to provide consistent, standards-based accessibility for web maps:

**Keyboard and screen reader navigation:** Current web maps have [highly variable accessibility](https://github.com/Malvoz/web-maps-wcag-evaluation/blob/master/README.md), creating inconsistent experiences for users of assistive technology. The MapML.js polyfill is actively researching standardized patterns for accessible map navigation (pan, zoom, feature traversal) with input from screen reader users and keyboard-only users. This research will inform the specification of accessible interaction models.

Related to this work, the [CSS Working Group is considering native pan/zoom primitives](https://github.com/w3c/csswg-drafts/issues/5275) for general web content. While MapML requires map-specific pan and zoom with coordinate reference system semantics (see [Non-Goals](#non-goals)), if the CSSWG develops a general-purpose primitive, MapML could potentially build upon it. The CSSWG proposal highlights performance benefits (compositor-thread rendering), accessibility improvements (standardized interaction models for assistive technology), and better device adaptation—benefits that would apply to maps as well. Today's JavaScript-based map implementations must hijack scroll and touch events, which degrades both performance and accessibility.

**Overlapping interactive content:** Maps uniquely present challenges with multiple layers of interactive features occupying the same visual space. The proposal addresses this through feature indexing and keyboard navigation patterns that allow users to discover and interact with all available content, even when features visually overlap.

**Progressive disclosure:** Map controls and features support progressive disclosure patterns, allowing users to navigate complex spatial information at their own pace without overwhelming cognitive load.

<h2 id="use-cases-and-requirements">Use Cases and Requirements</h2>

This proposal is being evaluated against the [Use Cases and Requirements for Standardizing Web Maps](https://maps4html.org/HTML-Map-Element-UseCases-Requirements/). Each element in the [MapML Element Reference](https://maps4html.org/web-map-doc/docs/elements/mapml-viewer/) includes a requirements table showing how it addresses specific use cases (for example, see the [requirements table for the `<map-extent>` element](https://maps4html.org/web-map-doc/docs/elements/extent/#requirements)).

<h2 id="alternative-approaches">Alternative Approaches</h2>

### SVGMap, Hyper-Layering Architecture (HLA) and Layers as Web Apps (LaWAs)

[SVGMap](https://discourse.wicg.io/t/vector-tiling-on-svgmap/3135) and its Hyper-Layering Architecture (HLA), developed by Satoru Takagi from W3C member KDDI Corporation, represents a complementary approach to web mapping that shares MapML's commitment to hypermedia principles and decentralization while taking a different architectural path.

**Shared Philosophy:**
- Both embrace HATEOAS (Hypertext as the Engine of Application State)
- Both prioritize decentralization and user choice over centralized platforms
- Both align with W3C Ethical Web Principles and the W3C Vision
- Both seek to make geographic information a first-class web citizen

**Architectural Differences:**

*SVGMap/HLA Philosophy:* Provides minimal, general-purpose hooks in the browser (SVG/CSS/Geo-coordinate mapping API) while keeping maps' domain-specific logic in user-land via "Layers as Web Apps" (LaWAs). In this architecture, geospatial resource providers would publish LaWAs—JavaScript applications that adapt data available via various geospatial protocols (WMS, WFS, GeoJSON, etc.) for rendering. The browser would load and execute these LaWAs to render the associated resources. This approach aims to keep mapping semantics and logic out of the browser core.

*MapML Philosophy:* Introduces minimal domain-specific map and location elements **into the browser core**, similar to how `<img>`, `<video>`, and `<audio>` brought media natively to HTML. Resources are declarative data (HTML/MapML documents) rather than executable code, following the same security model as other web content. This approach follows "paving the cowpaths"—standardizing patterns already widely used in geospatial web services while **enabling authors to apply skills they already possess** (HTML, CSS, JavaScript) to geographic data. Rather than requiring specialized geospatial expertise, MapML makes location and map data part of the standard web vocabulary, accessible to the same millions of developers who already build web pages.

**Key Distinction: Data vs. Code**

A fundamental difference lies in what gets transmitted from resource providers:
- **SVGMap/LaWAs:** Publishers provide executable JavaScript (LaWAs) that browsers load and run to render resources
- **MapML:** Publishers provide declarative data (MapML/HTML documents) that browsers parse and render using built-in capabilities

This distinction has important implications for security, browser implementer acceptance, and the web's evolution. Browsers have traditionally been cautious about automatically loading and executing third-party scripts from resource providers due to security risks.

**Potential for Integration:**

Despite these differences, we see potential for complementary use:

1. **MapML provides safe, declarative container and viewport management** - Handles extent, scale, projection, layering, and coordinate reference systems without requiring executable code from resource providers.

2. **SVGMap/HLA could provide opt-in advanced rendering** - Where authors explicitly choose to use SVGMap's approach, they could load LaWA adapters within layer scope. For example:

   ```html
   <map-layer label="My Layer, your data" checked>
     <map-script src="yourLaWA.js" type="module"></map-script>
   </map-layer>
   ```
   
   In this integration model, `<map-script>` would have different semantics from regular `<script>`:
   - **Scoped API access:** The LaWA receives a restricted `MapLayerContext` API providing access to viewport state, rendering methods for that specific layer, and layer-scoped event handlers - but not full DOM access to the page
   - **Sandboxed execution:** Runs in an isolated context (similar to Workers or Worklets) with message-passing to communicate with the layer, preventing XSS vulnerabilities
   - **CSP-controllable:** Page authors can control LaWA execution via Content Security Policy
   
   This approach combines explicit author opt-in (security) with dynamic protocol adaptation (flexibility), potentially bridging both architectural philosophies.

3. **SVG handles rendering where appropriate** - MapML's `text/mapml` media type can include `<svg>` elements, allowing SVGMap's vector rendering capabilities to work within MapML's coordinate framework.

4. **Shared hypermedia principles** - Both use hyperlinks to connect distributed services, enabling web-scale geographic data integration.

**Open Questions:**

The SVGMap/HLA proposal raises important architectural questions that require further discussion:

- **Security model:** How can browsers safely execute LaWAs from third-party resource providers without introducing security vulnerabilities? The web's security model distinguishes between:
  - **Explicit inclusion:** Page authors choose to load `<script src="https://cdn.com/library.js">` and accept responsibility for trusting that code
  - **Implicit execution:** Browsers automatically executing code based on resource URLs (e.g., if `<map-layer src="https://provider.com/data">` causes the browser to load and run JavaScript from that provider)
  
  The former is the current web model; the latter would be a significant departure raising questions about trust, control, and author intent.

- **DOM access and isolation:** How would LaWAs access and manipulate the map viewer DOM while maintaining origin isolation? The browser's security model (as seen with sandboxed SVG in `<img>`) typically prevents external resources from executing scripts with DOM access to the embedding page.

- **Author control:** Would LaWAs need to be explicitly loaded by page authors (like current JavaScript libraries) rather than automatically executed by browsers when referencing map resources?

- **Integration path:** Can the benefits of both approaches (MapML's declarative simplicity + SVGMap's rendering sophistication) be achieved while maintaining web security principles?

These are valuable questions for the broader web mapping standardization discussion.

**The Path Forward:**

A unified proposal incorporating both approaches could offer:
- **For simple use cases:** Declarative and accessible HTML map embedding (MapML) without JavaScript
- **For advanced use cases:** Dynamic LaWA adapters providing protocol flexibility
- **For all cases:** Shared minimal browser primitives that both can build upon

We believe browser vendors are more likely to engage with a unified geospatial web community than with competing proposals. Ongoing collaboration with the SVGMap community aims to reconcile these approaches into a coherent standardization path.

**Note:** SVGMap/HLA is successfully deployed in production systems, including [KDDI's disaster prevention Web GIS for local governments in Japan](https://www.kddi.com/corporate/sustainability/regional-initiative/bosaimapboard/), demonstrating real-world viability.

### JavaScript Mapping Libraries

Current state-of-the-art web mapping relies on JavaScript libraries:

**[MapLibre](https://maplibre.org/):** Modern, performant library with vector tile support and 3D capabilities. Backed by the Overture Maps Foundation and growing industry adoption.

**[Leaflet](https://leafletjs.com/):** Lightweight, widely-adopted library focused on mobile-friendly interactive maps. Simple API for common use cases.

**[OpenLayers](https://openlayers.org/):** Feature-rich library with extensive projection support and advanced capabilities. Steeper learning curve.

**Others:** Mapbox GL JS, Google Maps JavaScript API, Cesium (3D), and numerous specialized libraries.

**Strengths:**
- Mature, battle-tested codebases
- Rich ecosystems of plugins
- Sophisticated features and flexibility
- Active communities

**Limitations relative to MapML:**
- **High barrier to entry:** Requires JavaScript expertise and geospatial domain knowledge
- **Inconsistent accessibility:** Each library handles accessibility differently; many maps are inaccessible to screen readers
- **Performance overhead:** JavaScript parsing and execution on every page load
- **No native browser optimization:** Cannot benefit from browser-level optimizations for maps
- **Limited crawlability:** Search engines cannot easily index map content
- **Maintenance burden:** Sites must keep libraries updated; breaking changes require developer intervention

**Can MapML and libraries coexist?**

Yes. MapML's polyfill is built (today) *on* Leaflet, demonstrating that libraries can serve as polyfill implementation engines. The proposed HTML infrastructure would:
- Provide a declarative layer for simple use cases (80% of map embedding)
- Expose DOM APIs and events for JavaScript enhancement (the remaining 20%)
- Allow libraries to use native browser primitives when available, improving performance
- Enable simpler APIs when the browser handles coordinate transforms, projections, and viewport management

As noted by Andreas Hocevar (key contributor to OpenLayers) during the [2020 W3C/OGC Workshop](https://www.w3.org/2020/maps/report#executive-summary): with native rendering capabilities, mapping libraries could potentially **grow in capability** by shifting the burden of rendering to the browser, where authors can leverage native CSS tools for styling and visualization.

Authors could progressively enhance: start with declarative HTML maps, add CSS and JavaScript only where needed.

### Status Quo (No Standardization)

**Current situation:**
- Maps remain a specialized domain requiring JavaScript libraries
- Each site implements maps differently
- Accessibility varies widely (often poor)
- Location-based content exists in proprietary silos
- Search engines cannot index geographic information on the web
- Mobile web maps lag behind native app experiences

**Why change is needed:**

The status quo perpetuates the problems described in ["The Problem"](#the-problem) section: accessibility barriers, privacy concerns, centralization, and high complexity. Without standardization, the geographic web remains disconnected from the open web platform, limiting user choice and concentrating control with large platforms.

The web successfully standardized images, video, and audio. Maps deserve the same treatment.

<h2 id="stakeholder-feedback-opposition">Stakeholder Feedback / Opposition</h2>

**Community Engagement:**

This proposal has been developed through extensive community collaboration:

- **W3C/OGC Joint Workshop (2020):** Natural Resources Canada hosted a [joint workshop series](https://www.w3.org/2020/maps/) bringing together browser vendors, geospatial community members, and standards bodies. The [workshop report](https://www.w3.org/2020/maps/report) documented consensus on key challenges and potential approaches.

- **Evidence-Based Research (2022):** [Bocoup conducted research](https://www.bocoup.com/blog/no-neutral-map) analyzing HTTP Archive data and surveying developers, validating the problem space and recommending an iterative standardization approach. This research confirmed that 16% of web pages use maps—more than `<video>` or `<picture>` elements—and identified significant accessibility and authoring challenges.

- **State of HTML 2025 Survey:** MapML was included as a question in the State of HTML 2025 survey, indicating ongoing community interest.

**Implementer Signals:**

- **Browser vendors:** No formal positions published yet. Seeking feedback through W3C TAG review and standards process.
- **Geospatial community:** Positive engagement through OGC and Maps for HTML Community Group. GeoServer (widely-deployed open source platform) has implemented the MapML extension module.
- **JavaScript library developers:** Ongoing collaboration; polyfill built on Leaflet demonstrates coexistence model.

**Path Forward:**

We welcome concrete, actionable feedback from all stakeholders. The proposal follows an iterative approach with polyfill validation, use case documentation, and community group iteration. Browser implementer participation and feedback are essential to refine the proposal for standardization.

<h2 id="references-and-acknowledgements">References</h2>

**Key Resources:**
- [MapML Specification](https://maps4html.org/MapML/spec/)
- [MapML Element Reference](https://maps4html.org/web-map-doc/docs/elements/mapml-viewer/)
- [MapML.js](https://www.npmjs.com/package/@maps4html/mapml/v/latest) - polyfill implementation available via npm
- [GeoServer MapML Extension](https://docs.geoserver.org/latest/en/user/extensions/mapml/index.html) - demonstrates server-side MapML adoption
- [Use Cases and Requirements for Standardizing Web Maps](https://maps4html.org/HTML-Map-Element-UseCases-Requirements/)
- [W3C/OGC Joint Workshop Series on Maps for the Web](https://www.w3.org/2020/maps/) (2020) - [Workshop Report](https://www.w3.org/2020/maps/report)
- [WCAG Evaluation of Web Maps](https://github.com/Malvoz/web-maps-wcag-evaluation/blob/master/README.md)
- [Bocoup Web Maps Research](https://www.bocoup.com/blog/no-neutral-map) (2022) - HTTP Archive analysis showing 16% of web pages use maps

**Acknowledgements:**

This proposal represents the collaborative efforts of the [W3C Maps for HTML Community Group](https://www.w3.org/community/maps4html/). We gratefully acknowledge the contributions of all community group participants, past and present, whose insights, code contributions, use case documentation, and sustained engagement have shaped this work. Special thanks to Natural Resources Canada for hosting the W3C/OGC Joint Workshop Series, to Bocoup for their evidence-based research, and to Simon Pieters for his thorough review and advocacy on behalf of the community with the CSS Working Group.

