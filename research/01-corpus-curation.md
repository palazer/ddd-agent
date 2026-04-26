# A curated DDD corpus for an AI sparring agent

## Executive summary

The free, AI-ingestible canon of Domain-Driven Design is smaller and more tractable than its reputation suggests: roughly **150–200k tokens of first-party Markdown/PDF** covers the entire Evans/Vernon/Fowler/DDD-Crew foundation, and a further ~300k of transcripts from DDD Europe, Virtual DDD and Explore DDD covers nearly every modern strand (EventStorming, sociotechnical, residuality, CUPID, Wardley, Team Topologies). The sharpest gap is domain-specific literature: **published DDD case studies on B2B marketplaces, multi-sided platforms, offer/pricing engines, and EV-charging/roaming are genuinely thin** — there is no Evans-scale treatment of this space. The richest vein is Mathias Verraes' blog (pricing, temporal modelling, bounded-context splits), Vlad Khononov's Marketnovus post-mortem, and a handful of engineering blogs (Otto, Walmart, Stripe, Fiverr). The practical implication for the sparring agent: saturate it with strategic/tactical canon and sociotechnical heuristics, then deliberately supplement with adjacent-industry case studies (insurance quoting, payment gateways, ACL patterns) rather than hoping for a direct match.

Tiering rule used below: **Tier 1** = must-ingest foundation; **Tier 2** = strong complement; **Tier 3** = modern voices / specialised techniques; **Tier 4** = B2B-marketplace-specific material; plus two appendices for anti-patterns and ingestion strategy.

---

## Tier 1 — Foundation (free, AI-ingestible first)

Ingest these first. All except the commercial books are free, selectable-text, and permissively licensed.

### The free canonical core

**Eric Evans — *DDD Reference: Definitions and Pattern Summaries* (v2015-03).** Free PDF, CC BY 4.0, ~50–70 pp. Landing: https://www.domainlanguage.com/ddd/reference/ — Direct PDF: https://www.domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf. **AI-ingestion: High** (selectable text, CC-licensed, derivative-friendly). This is Evans' own condensation of the Blue Book into pattern summaries — **ground-truth definition dictionary** for the sparring agent. Also on the same site: *Getting Started With DDD When Surrounded By Legacy Systems* (2013 PDF) — directly relevant for brownfield EV-charging environments.

**Vaughn Vernon — *Effective Aggregate Design* (three-part essay, 2011).** Free PDFs on dddcommunity.org, CC BY-ND 3.0, ~45 pp total. Part I: https://www.dddcommunity.org/wp-content/uploads/files/pdf_articles/Vernon_2011_1.pdf · Part II: https://www.dddcommunity.org/wp-content/uploads/files/pdf_articles/Vernon_2011_2.pdf · Part III: https://www.dddcommunity.org/wp-content/uploads/files/pdf_articles/Vernon_2011_3.pdf. **AI-ingestion: High.** Encodes the four rules — *model true invariants in consistency boundaries, design small aggregates, reference other aggregates by identity, update via eventual consistency*. **Non-negotiable tactical reference.**

**Martin Fowler bliki cluster.** Clean HTML, ~30 KB combined text, free. Seven must-ingest entries: BoundedContext (https://martinfowler.com/bliki/BoundedContext.html), DomainDrivenDesign, DDD_Aggregate, CQRS (https://martinfowler.com/bliki/CQRS.html), EventSourcing (https://martinfowler.com/eaaDev/EventSourcing.html), AnemicDomainModel, UbiquitousLanguage. Plus the disambiguation essay **"What do you mean by Event-Driven?"** (https://martinfowler.com/articles/201701-event-driven.html) — distinguishes Event Notification, Event-Carried State Transfer, Event Sourcing, and CQRS. **AI-ingestion: High** across the board.

**Evans & Fowler — Specification pattern paper.** Free PDF, ~16 pp: https://martinfowler.com/apsupp/spec.pdf. Origin of `isSatisfiedBy()` composition, covering hard-coded, parameterized, composite, subsumption variants. **Tier 1 tactical** for rich domain rules (pricing eligibility, offer constraints — directly relevant to the user's domain).

**Alistair Cockburn — Hexagonal Architecture / Ports & Adapters.** Original HTML at https://alistair.cockburn.us/hexagonal-architecture/, plus 2023 Budapest slides on alistaircockburn.com. **AI-ingestion: High** for the original article. Pair with Juan Manuel Garrido de Paz's endorsed handbook at https://jmgarridopaz.github.io/ for unambiguous driver/driven-port semantics (not the commonly-mis-read "layered" interpretation).

**Robert C. Martin — The Clean Architecture** (2012, https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) plus companions "A Little Architecture", "Screaming Architecture", "Clean Micro-service Architecture". Canonical dependency-rule vocabulary.

### The commercial canon (paid; ingest via legitimately-acquired copies)

**Eric Evans — *Domain-Driven Design: Tackling Complexity in the Heart of Software* ("Blue Book", 2003, ~560 pp).** Publisher: https://www.informit.com/store/domain-driven-design-tackling-complexity-in-the-heart-9780321125217. **AI-ingestion: Low** (DRM ePub). The DDD Reference above is the recommended free substitute for the sparring agent's definitional grounding; the Blue Book remains essential for narrative depth (Supple Design, Strategic Distillation, the cargo-shipping case).

**Vaughn Vernon — *Implementing Domain-Driven Design* ("Red Book", 2013, ~656 pp).** Publisher: https://www.informit.com/store/implementing-domain-driven-design-9780321834577 · Free sample: https://ptgmedia.pearsoncmg.com/images/9780321834577/samplepages/0321834577.pdf. **AI-ingestion: Low** for full text, Medium for sample. Canonical tactical companion — **Aggregates, Domain Events, CQRS, Hexagonal+DDD, Long-Running Processes/Sagas, Event Sourcing, Integration**. Most-cited implementation reference.

**Vaughn Vernon & Tomasz Jaskuła — *Strategic Monoliths and Microservices* (2021, ~320 pp).** https://www.informit.com/store/strategic-monoliths-and-microservices-driving-innovation-9780137355464. **Tier 1 for the user's EA role** — explicitly connects DDD strategic design to monolith-vs-microservices decisioning, Event Storming, Context Mapping, Conway's Law. Free sample chapter PDF at ptgmedia.pearsoncmg.com.

**Vlad Khononov — *Learning Domain-Driven Design* (O'Reilly, 2021, ~340 pp).** https://www.oreilly.com/library/view/learning-domain-driven-design/9781098100124/. **AI-ingestion: Medium** (paywalled). The best modern introduction — integrates strategic + tactical, subdomain types, bounded contexts, event sourcing, architecture patterns. **Promote to Tier 1** alongside Red Book for a modern sparring baseline; the Marketnovus appendix case study is directly useful.

### The free AI-ingestible GitHub scaffolding — **DDD Crew**

The DDD Crew org (https://github.com/ddd-crew, 17 public repos, most CC-BY-SA-4.0) is **the highest-leverage wholesale ingestion target**. Combined Markdown-only footprint is ~55–95k tokens — fits easily in a single long-context window. Priority repos in descending order of sparring utility:

1. **ddd-starter-modelling-process** (https://github.com/ddd-crew/ddd-starter-modelling-process, ~5.8k ★, active Dec 2025). Step-by-step strategic→tactical process; the richest narrative asset in the org. **AI-ingestion: High.**
2. **bounded-context-canvas** (~2k ★). Structured BC design template with prose README; canvas itself PDF/PNG. **High for README, Medium for canvas.**
3. **context-mapping** (~1.8k ★). Cheatsheet + pattern starter kit.
4. **aggregate-design-canvas** (~808 ★). Aggregate design structure.
5. **core-domain-charts** (~609 ★). Strategic differentiator identification.
6. **domain-message-flow-modelling** (~403 ★). Commands/events/queries between BCs — **directly applicable to EV-charging message flows**.
7. **eventstorming-glossary-cheat-sheet** (~960 ★). Terminology glossary; primary cheat sheet is PDF (run PDF text extract, not OCR).
8. **welcome-to-ddd** (~1.5k ★). Fundamentals.
9. **free-ddd-learning-resources** (~797 ★). Curated link list.
10. **ai-ddd-prompts-and-rules** (active 2025). Prompt library for AI coding assistants — meta-relevant to the user's project.
11. **debiasing-decisions-toolkit** (active 2026). Behavioural checklists for tech-leadership decisions.
12. **como-prep-canvas** (active 2025). Collaborative modelling workshop prep.

Skip: *virtual-modelling-templates* (Miro templates, image-heavy), *ddd-crew.github.io* (duplicate of source repos), *strategic-architecture-building-blocks* (marked obsolete). Note: `virtual-ddd-meetup-examples` does **not** exist; the adjacent resource is `Virtual-Domain-driven-design/virtual-domain-driven-design` (Gatsby site for virtualddd.com).

### Worked example: **Kamil Grzybek's modular monolith**

**Kamil Grzybek — Modular Monolith blog series + reference .NET implementation.** Blog: https://www.kamilgrzybek.com/blog/series/modular-monolith · Repo: https://github.com/kgrzybek/modular-monolith-with-ddd (~12.5k ★, MIT). **AI-ingestion: High for blog + README**, Medium for full C# codebase. The most rigorous freely-available worked example combining Aggregates, Domain Events, CQRS, the Outbox pattern, and cross-module messaging. **Tier 1 specifically for the user's EA context** — a modular monolith is a credible target architecture for EV-charging B2B platforms.

---

## Tier 2 — Strong complement (adjacent strands & tactical depth)

### EventStorming

**Alberto Brandolini — *Introducing EventStorming* (Leanpub, in progress).** https://leanpub.com/introducing_eventstorming · companion: https://www.eventstorming.com/book/. **AI-ingestion: Medium** (Leanpub PDF, paid, ~70% complete with some truncated chapters — treat as a living document, not finished canon). Still the authoritative source from the method's inventor.

**Brandolini — "Strategic Domain-Driven Design with Context Mapping"** (InfoQ 2009, https://www.infoq.com/articles/ddd-contextmapping). Free HTML. Canonical practitioner article on context mapping patterns; cited by Vernon, Team Topologies, Evans. **High ingestion.**

**Brandolini's blog** (http://ziobrando.blogspot.com/) and Medium (https://medium.com/@ziobrando) — foundational posts on Big Picture vs Design-Level EventStorming.

### Domain Storytelling & Example Mapping

**Stefan Hofer & Henning Schwentner — *Domain Storytelling* (Addison-Wesley Signature Series, 2022).** https://www.oreilly.com/library/view/domain-storytelling-a/9780137458974/ · community hub https://domainstorytelling.org/book · awesome list https://github.com/hofstef/awesome-domain-storytelling. Complements EventStorming with a narrative-driven approach; foreword by Nick Tune. **Ingestion: Medium** (paywalled) but community site is free/High.

**Matt Wynne — "Introducing Example Mapping"** (https://cucumber.io/blog/bdd/example-mapping-introduction/). The seminal short article (~2,000 words, free HTML). Pair with Steve Tooke's "Your first Example Mapping session" (https://cucumber.io/blog/bdd/your-first-example-mapping-session/). **High ingestion.**

### Event-driven architecture & CQRS/ES

**Ben Stopford — *Designing Event-Driven Systems* (Confluent/O'Reilly, 2018, ~166 pp).** **Free PDF: https://www.confluent.io/wp-content/uploads/confluent-designing-event-driven-systems.pdf** (landing: https://www.confluent.io/resources/ebook/designing-event-driven-systems/). **AI-ingestion: High** — one of the best free paid-quality texts in the corpus. Foreword by Sam Newman; covers event sourcing + CQRS + "inside-out DB".

**Greg Young — *CQRS Documents* (2010, ~55 pp free PDF).** https://cqrs.files.wordpress.com/2010/11/cqrs_documents.pdf. **High ingestion.** The original CQRS manual; commands/events/event-sourcing formalised by the author. Pair with **Greg Young — *Versioning in an Event Sourced System*** (Leanpub, free-to-read online at https://leanpub.com/esversioning/read) — the definitive treatment of schema versioning, upcasting, copy-transform.

**Udi Dahan — "Clarified CQRS" (2009, https://udidahan.com/2009/12/09/clarified-cqrs/)** and **"When to Avoid CQRS" (2011, https://udidahan.com/2011/04/22/when-to-avoid-cqrs/)**. Long-form blog, free, High ingestion. The essential counterweight: **CQRS ≠ Event Sourcing**, and a catalogue of when *not* to use it. Also: "Race Conditions Don't Exist" (2010) on distinguishing business-time from technical-time races. Recent NDC Oslo 2023 talk "CQRS Pitfalls and Patterns" exists with transcript on particular.net/videos/cqrs-pitfalls-and-patterns.

**Adam Bellemare — *Building Event-Driven Microservices* (O'Reilly, 2020; 2nd ed 2024/2025, ~321 pp).** https://www.oreilly.com/library/view/building-event-driven-microservices/9781492057888/. **Ingestion: Medium** (paywalled). Schemas, state stores, outbox, CQRS at microservice scale. Companion: *Building an Event-Driven Data Mesh* (2023) extends the thinking into data-product territory.

### Tactical-code architecture

**Jeffrey Palermo — Onion Architecture** (2008 original four-part series, https://jeffreypalermo.com/tag/onion-architecture/). Bridge between Ports & Adapters (2005) and Clean Architecture (2012). Free HTML, High ingestion.

**Scott Millett & Nick Tune — *Patterns, Principles, and Practices of Domain-Driven Design* (Wrox, 2015, ~800 pp).** https://www.wiley.com/en-us/Patterns%2C+Principles%2C+and+Practices+of+Domain+Driven+Design-p-9781118714706. **The most comprehensive .NET-flavoured DDD reference.** The free Leanpub working draft (https://leanpub.com/Practicing-DDD) contains Part I only; Parts II–IV are in the Wrox book. Ingestion Medium-High for free portion, Low for full.

**Sam Newman — *Building Microservices 2nd ed* (2021, ~616 pp)** and ***Monolith to Microservices* (2019)**. The DDD-relevant chapters are Building Microservices Ch.2 ("How to Model Microservices" — coupling types, Ubiquitous Language, Aggregate, Bounded Context) and Ch.3 ("Splitting the Monolith"), plus Monolith-to-Microservices Ch.1 ("Just Enough DDD") and Ch.2 ("Planning a Migration" — Event Storming + Strangler Fig). **Ingestion: Medium** (paywalled O'Reilly).

### Architecture Decision Records

**Michael Nygard — "Documenting Architecture Decisions"** (2011, https://www.cognitect.com/blog/2011/11/15/documenting-architecture-decisions). Free blog, CC0-ish, ~1,000 words. The canonical Title/Context/Decision/Status/Consequences format. Pair with **adr.github.io** (community hub with MADR, Y-Statement, Tyree/Akerman templates), **github.com/npryce/adr-tools** (CLI, GPL; ~5.4k ★), and **github.com/joelparkerhenderson/architecture-decision-record** (~14.3k ★, Markdown-first, excellent for wholesale ingestion).

---

## Tier 3 — Modern voices & specialised techniques

### Strategic / sociotechnical

**Nick Tune — Substack "Strategy, Architecture, Continuous Delivery, and DDD"** (https://nicktune.substack.com/) and Medium (https://medium.com/nick-tune-tech-strategy-blog, handle @NTCoding). **Tier 1-adjacent for strategic sparring.** Must-read posts: *Strategic DDD Remote Collaboration Toolkit*, *Legacy Architecture Modernisation with Strategic DDD*, *Architecture & DDD Kata: Online Car Dealership*, *Delivericious Kata*, *ddd vs DDD*. Also his GitHub (https://github.com/NTCoding) hosts *modernization-strategy-selector* (CC-BY-SA-4.0), *living-architecture*, *claude-skillz* (Markdown skill specs directly relevant to the sparring-agent build), and *prod-domain-biz-arch-building-blocks*. **Ingestion: High** for all free content.

**Matthew Skelton & Manuel Pais — *Team Topologies* (IT Revolution, 2019; 2nd ed Sept 2025).** https://teamtopologies.com/book. **Free CC-BY-SA-4.0 resources** at https://teamtopologies.com/resources and https://teamtopologies.com/key-concepts — these are High-ingestion. References collection on GitHub: https://github.com/TeamTopologies/Team-Topologies-Book-References. Essential for Conway's-Law reasoning in any strategic DDD conversation.

**Simon Wardley — *Wardley Maps* (Medium book, CC BY-SA 4.0, free).** Medium: https://medium.com/wardleymaps. Community-produced consolidated PDF/MOBI: https://github.com/andrewharmellaw/wardley-maps-book · Leanpub: https://leanpub.com/wardley-maps. **Ingestion: High** (free CC-licensed). Pair with **Ben Mosior's LearnWardleyMapping.com** (https://learnwardleymapping.com/) — more structured pedagogy, the community consensus is that Mosior's materials are more approachable than Wardley's original blog-compilation format.

**Eduardo da Silva — esilva.net.** https://esilva.net/ and InfoQ archive https://www.infoq.com/profile/Eduardo-da-Silva/. Most structured writing tying **sociotechnical architecture to DDD + Team Topologies**. Entry points: the *Introduction to Sociotechnical Architecture* series (Part 2 — Traits & Strategies), and the TLA_insights *Architecture Topologies* piece. High ingestion.

**Trond Hjorteland.** Speaker hub https://sessionize.com/trond-hjorteland/, slides https://www.slideshare.net/trondhr. Rooted in Emery/Trist open-systems tradition; best single talk: *Sociotechnical Systems Design for the "Digital Coal Mines"* (DDD Europe 2021, https://www.youtube.com/watch?v=zRcnIcaZO_U).

**Xin Yao.** Speakerdeck decks: https://speakerdeck.com/xinyao/dddeu2023-keynote-systems-thinking-in-large-scale-modeling and https://speakerdeck.com/xinyao/domain-driven-architecture-at-scale-ddd-eu-2022. Sessionize: https://sessionize.com/xin-yao. Large-scale sociotechnical modelling. Note: **residuality theory** is Barry M. O'Reilly's work (DDD Europe 2022 "Introduction to Residuality Theory": https://www.youtube.com/watch?v=MZytZW_k-9Y; with Cyrille Martraire 2023: https://www.youtube.com/watch?v=JUF6eiS0cF0), sometimes conflated with Yao's sociotechnical work — ingest both.

**Kenny Baas-Schwegler (baasie.com), Evelyn van Kelle (evelynvankelle.com), Gien Verschatse — *Collaborative Software Design: How to Facilitate Domain Modeling Decisions* (Manning, 2024).** https://www.manning.com/books/collaborative-software-design. **The best single-volume synthesis** bridging EventStorming + Example Mapping + Domain Storytelling + Wardley Mapping + facilitation heuristics + sociotechnical framing. Forewords by Montalion and Hjorteland. **Ingestion: Medium** (paywalled Manning). Authors' blogs are free/High.

### Code-level & philosophical

**Dan North — "CUPID: for joyful coding"** (https://dannorth.net/blog/cupid-for-joyful-coding/) + back-story (https://dannorth.net/2021/03/16/cupid-the-back-story/). Free HTML, High ingestion. The **properties-over-principles alternative to SOLID** — composable, Unix-philosophy, predictable, idiomatic, domain-based. Still evolving; treat as living writing.

**Dave Snowden — Cynefin framework.** Intro at https://thecynefin.co/about-us/about-cynefin-framework/ · wiki https://cynefin.io/wiki/Cynefin · HBR article "A Leader's Framework for Decision Making" (Snowden & Boone, 2007). Caveat: domain labels have evolved over time (known/knowable → simple/complicated → clear/complicated); cite sources with their era. Essential for reasoning about domain complexity when the sparring agent should *refuse* a tactical answer.

**Gojko Adzic — *Impact Mapping* (2012).** Community site https://www.impactmapping.org/ · **free sample PDF**: https://www.impactmapping.org/assets/impact_mapping_20121001_sample.pdf. High ingestion. Plus ***Specification by Example*** (Manning, 2011, Jolt Award winner): https://www.manning.com/books/specification-by-example.

**Rebecca Wirfs-Brock — Responsibility-Driven Design.** Hub: https://www.wirfs-brock.com/Design.html · **free primer PDF**: https://www.wirfs-brock.com/PDFs/Responsibility-Driven.pdf · personal site https://wirfs-brock.com/rebecca/ (papers, IEEE Software columns, heuristics, "Discovering Your Software Umwelt"). She coined "-driven design"; RDD is a DDD precursor emphasising role stereotypes and collaborators. Her joint DDD Europe 2022 talk with Verraes — *Critically Engaging with Models* (https://www.youtube.com/watch?v=ecW1jMhIt2k) — is essential.

### Mathias Verraes — verraes.net

Free static HTML, High ingestion across the board. Cluster of high-signal posts that together form a **pattern language** for messaging and temporal DDD:

- *DDD and Messaging Architectures* (2019, https://verraes.net/2019/05/ddd-msg-arch/) — index of 15+ messaging/event-sourcing patterns.
- *Temporal Modelling* (https://verraes.net/2019/06/talk-temporal-modelling/) — time as first-class citizen.
- *Messaging Flavours* — informational vs interrogatory vs imperative.
- *Eventsourcing Patterns* series — multi-temporal events, decision tracking, ghost context migration.
- *No, Your Domains and Bounded Contexts Don't Map 1 on 1* (Aug 2025, https://verraes.net/2025/08/domain-and-bounded-contexts-dont-map-one-on-one/) — explicit pushback against the "1 BC per subdomain" myth; contains the online-retailer pricing case (see Tier 4).
- *Splitting a Domain Across Multiple Bounded Contexts* (2021, with Wirfs-Brock) — rate-of-change heuristics.
- *Tensions when Designing Evolvable Bounded Contexts* (2021); *Emergent Contexts through Refinement* (2019); *Context Mapping: Life Expectancy* (2015); *Buzzword-free Bounded Contexts* (2014).

### Vlad Khononov — vladikk.com

Blog (https://vladikk.com/) plus ***Balancing Coupling in Software Design* (Addison-Wesley, 2024, Vernon Signature Series).** https://www.informit.com/store/balancing-coupling-in-software-design-universal-design-9780137353538. Formalises **Balance = (Strength XOR Distance) OR NOT Volatility**, explicitly integrating Cynefin and DDD subdomain-volatility reasoning. **A Tier 1 modern text despite being paid** — the coupling heuristics generalise directly to bounded-context design decisions in a B2B marketplace.

### Michael Plöd — *Hands-on Domain-driven Design by example*

Leanpub, continuously updated: https://leanpub.com/ddd-by-example. Medium ingestion (Leanpub PDF, paid with free preview). Large real-estate-loans worked example combining Event Storming, Domain Storytelling, Context Map, and BC→microservice mapping. One of the few book-length strategic DDD case studies in an adjacent B2B domain.

### DDD France — Maxime Sanglan-Charlier

Active in the DDD France community; best surfaced content is conference talks rather than a standalone blog corpus — ingest via DDD Europe/Virtual DDD transcripts (below).

---

## Tier 4 — B2B marketplace / offer management resources

**Honest assessment up front: this layer is the thinnest in published DDD literature.** There is no canonical "marketplace DDD book" equivalent to Evans or Vernon. The best material is scattered across engineering blogs, single conference talks, and adjacent industries (insurance, payments). Ingest broadly and cross-apply.

### Core case studies

**Mathias Verraes — pricing bounded-context case (Aug 2025).** https://verraes.net/2025/08/domain-and-bounded-contexts-dont-map-one-on-one/. **The most directly relevant single article for the user's domain.** An online retailer has a *single* "Pricing" core domain (webscraping+ML system vs manual pricing/discount system) but Verraes argues for **at least four bounded contexts** driven by differences in tech stack, team skills, modelling language, and historical evolution. Directly contradicts the "1 microservice per BC per subdomain" heuristic repeated in introductory material.

**Vlad Khononov — Marketnovus case study** (Appendix A of *Learning Domain-Driven Design*, 2021). Five bounded contexts of a defunct marketing-outsourcing startup — Marketing, CRM, Event Crunchers, Bonuses, Marketing Hub — as a post-mortem of applied DDD. Rare **full-lifecycle** documentation including what failed.

**Eliran Friedman — "Introducing Fiverr Logo Maker: A DDD Case Study"** (Fiverr Engineering Medium, https://medium.com/fiverr-engineering/domain-driven-design-case-study-introducing-fiverr-logo-maker-94f0339e41aa). Integrating a new product into an existing marketplace. Three domains (Fiverr marketplace/auth/payments, Makers project management, Logo Editor), with the same `User` entity represented differently per BC; REST for sync, event bus for async. **Ingestion: High** (Medium HTML; may require a cached version if behind soft-paywall).

**Chandra Ramalingam (Walmart Global Tech) — "Building Domain-Driven Microservices"** (https://medium.com/walmartglobaltech/building-domain-driven-microservices-af688aa1b1b8). Uses Pricing Context explicitly; important nuance that **aggregates don't always map 1-to-1 to microservices** when domain understanding is incomplete.

**Capital One Tech — "Event Storming for Microservice Architecture"** (https://www.capitalone.com/tech/software-engineering/event-storming-for-microservice-architecture/). e-Commerce example with "promotional offers → checkout" BC boundary; Order aggregate appearing in Shopping Cart, Order Capture, Order Fulfillment as distinct microservices.

**Otto (German retailer) — "On monoliths and microservices"** (https://www.otto.de/jobs/en/technology/techblog/blogpost/on-monoliths-and-microservices_2015-09-30.php). Vertical decomposition by business domain; empirical lesson that attempting shared libraries (e.g., `product`) across verticals leaked coupling — reinvention proved better for autonomy. A rare **negative-result** engineering post.

**Stripe — PaymentIntent API redesign** (https://news.alvaroduran.com/p/stripe-made-the-obvious-choice-when). Original `Token`+`Charge` model (well-modelled for card payments) broke when async/push payment methods arrived. Rewrote to `PaymentIntent`+`PaymentMethod`. Lesson: **willingness to throw out a good model when the domain expands** — directly applicable to EV-charging payment evolution (AFIR, roaming clearing).

### Insurance quoting (the classic adjacent domain)

Insurance quote/offer is the most heavily-written DDD case-study domain and transfers well to EV-charging tariffs/offers. The best material is conference-talk-shaped rather than book-shaped: search the **DDD Europe YouTube channel** for "insurance" and "quote" (Lemonade-era modelling talks, plus speakers like Cyrille Martraire who has published on insurance DDD via Arolla/Carbon-IT).

### Anti-Corruption Layer in B2B payment / roaming / EDI contexts

Published DDD writing on ACL in payment contexts is scarce in long form, but the pattern is extensively worked in: (a) Verraes' messaging patterns index (above), (b) Vernon Red Book Ch. 3 integration chapter, and (c) Uncle Bob's "Clean Micro-service Architecture" (https://blog.cleancoder.com/uncle-bob/2014/10/01/CleanMicroserviceArchitecture.html). Practitioner pattern is consistent: ACL wraps gateway-specific formats/error codes/business rules (Facade + Adapter + Translator inside), core domain stays clean, change locus is the ACL alone. **For OCPI/OCPP and AFIR-regulated roaming clearing, treat each external protocol as a Published Language requiring its own ACL** — a sparring agent should steer consistently to this pattern.

### Conference talk library (marketplace/offer/insurance filter)

Specific high-signal talks already confirmed:
- **Brandolini — "DDD tales from ProductLand"** (Explore DDD 2024, https://www.youtube.com/watch?v=hVK5swIL1_g) — product-catalogue modelling.
- **Udi Dahan — "If (domain logic) then CQRS, or Saga?"** (DDD Europe 2017, https://www.youtube.com/watch?v=fWU8ZK0Dmxs) — direct applicability to long-running offer/booking flows.
- **Nick Tune — "Defining Socio-Technical Boundaries with Bounded Context Canvas"** (https://www.youtube.com/watch?v=hYvGD1frbfM) — organisational mapping for platform businesses.

**Pattern-conference papers (EuroPLoP/PLoP) on marketplace/catalogue/pricing are genuinely sparse** — the pattern language literature skews toward payment-processing and enterprise integration rather than marketplaces specifically. The Gregor Hohpe / Bobby Woolf *Enterprise Integration Patterns* catalogue (https://www.enterpriseintegrationpatterns.com/) is the closest analogue and remains valuable as ACL/messaging background.

### EV-charging / OCPI / AFIR specifically

**The DDD literature on EV charging, OCPI, OCPP, and roaming clearing is effectively zero.** No dedicated books, no conference talks of note, no widely-cited blog posts. The practical approach for the sparring agent is to treat this as a **greenfield DDD problem requiring first-principles application of Tier 1–3 material**, with analogues imported from: (a) telecom roaming clearing patterns, (b) insurance quoting, (c) payment-gateway ACL patterns, and (d) marketplace multi-sided-platform literature above. **This is a genuine gap** — and an opportunity for the user to publish original case-study material.

### Notable gap call-outs

Searches on combinations of "domain-driven design" + [B2B platform / multi-sided / pricing engine / auction / OCPI / AFIR] return thin results and many SEO listicles of low quality (GeeksforGeeks, Rootquotient, generic LinkedIn "advice" articles). These should be **excluded from the sparring agent's corpus** — they frequently overstate the "1 microservice per BC" rule and conflate subdomains with bounded contexts. Verraes' 2025 pricing article is explicitly a rebuttal to this genre.

---

## Appendix A — Anti-patterns and critical perspectives

**Martin Fowler — "AnemicDomainModel" (Nov 2003, https://martinfowler.com/bliki/AnemicDomainModel.html).** The foundational anti-pattern post. A sparring agent must actively *diagnose* anemic models in user-supplied code, not just recognise the term.

**Greg Young — "The Art of Destroying Software" (2014/2017).** Video: https://www.youtube.com/watch?v=1FPsJ-if2RU. Full Whisper transcript at https://dbohdan.com/art-of-destroying-software (**High ingestion**). Core thesis: *optimise code for deletion; no component should take more than a week to rewrite* — a Conway-flavoured argument for small, replaceable bounded contexts. Companion: **"8 Lines of Code" (QCon London 2013)**, https://www.infoq.com/presentations/8-lines-code-refactoring/ (InfoQ has slides and partial transcript). Direct critique of DI/ORM/framework "magic" that bloats enterprise-DDD implementations.

**Three Dots Labs — "Introduction to DDD Lite: When Microservices in Go Are Not Enough"** (https://threedots.tech/post/ddd-lite-in-go-introduction/). Pragmatic framing of "tactical DDD without heavyweight ceremony" — the cleanest answer to "when not to use full DDD". Go-flavoured but language-agnostic. **High ingestion.**

**Eric Evans 2019+ retrospective talks** — particularly *What is DDD* (DDD Europe 2019, https://www.youtube.com/watch?v=pMuiVlnGqjk), *Language in Context* (2019, https://www.youtube.com/watch?v=xyuKx5HsGK8), and *DDD & LLMs* (DDD Europe 2024, https://www.youtube.com/watch?v=lrSB9gEUJEQ). Evans himself has moderated many of the book's stronger claims; the sparring agent should know these updates.

**Devil's-advocate material (include sparingly, Tier-3, not as guidance):** Tony Marston's "Why I Don't Do Domain Driven Design" (https://www.tonymarston.net/php-mysql/dont-do-domain-driven-design.html) and Peter Vogel's "Domain-Driven Design: Everything You Believe Is Wrong!" (https://visualstudiomagazine.com/articles/2015/07/01/domain-driven-design.aspx). Useful for exposing the agent to common objections; do **not** allow them to inform affirmative recommendations.

---

## Appendix B — Ingestion strategy

### Format-quality ranking

**Top-tier (wholesale ingest, Markdown or clean HTML):** Evans *DDD Reference* PDF, Vernon *Effective Aggregate Design* PDFs, all Fowler bliki, all DDD-Crew Markdown READMEs, Nick Tune Substack/Medium, Mathias Verraes blog, Udi Dahan blog, Kamil Grzybek blog, Dan North blog, esilva.net, baasie.com, Team Topologies free resources, LearnWardleyMapping.com, awesome-ddd (heynickc), joelparkerhenderson/architecture-decision-record. Clean text; license-permissive (mostly CC-BY-SA 4.0 or CC BY 4.0); minimal OCR risk.

**PDF (text-extractable, not OCR):** Stopford *Designing Event-Driven Systems*, Young *CQRS Documents*, Young *Versioning in an Event Sourced System* (Leanpub web-read), Evans/Fowler Specification paper, Wirfs-Brock RDD primer, ddd-crew canvases. Extract with `pdftotext -layout` or `pymupdf`; selectable text, no OCR pipeline needed.

**Paid books (legitimate acquisition required):** Evans Blue Book, Vernon Red/Distilled/Strategic Monoliths, Khononov Learning DDD/Balancing Coupling, Millett & Tune, Hofer & Schwentner, Skelton & Pais Team Topologies, Baas/van Kelle/Verschatse Collaborative Software Design, Newman Building Microservices/Monolith to Microservices, Bellemare, Plöd (Leanpub), Brandolini (Leanpub in-progress). Use publisher-provided sample chapters (links in Tier 1–3 above) for the "free" demo layer; full content requires licensed copies.

**Video/talks (transcript pipeline).** Recommended stack: (1) `yt-dlp --flat-playlist --dump-json` per channel to enumerate; (2) `youtube-transcript-api` (Python) per video-ID for plaintext; (3) normalise to JSONL of `{video_id, title, speaker, conference, year, transcript}`, chunk to ~500–1,000 tokens with ~10% overlap for embedding. **Priority order:** InfoQ DDD section (https://www.infoq.com/DomainDrivenDesign/presentations/ — **human-edited transcripts**, best-in-class) → DDD Europe (channel UC3PGn-hQdbtRiqxZK9XBGqQ, ~400+ talks) → Explore DDD (https://www.youtube.com/@ExploreDDD) → Virtual DDD (https://virtualddd.com, channel UCSoUh4ikepF3LkMchruSSaQ) → GOTO (DDD-filtered) → NDC (DDD-filtered) → KanDDDinsky (watch for mixed-German captions). Auto-captions on all major channels are >95% usable on native-English speakers; degrade on older 2016–2018 uploads and on panel/Q&A sessions.

### Token budget for a fully-loaded sparring corpus

A realistic target: **~300–500k tokens of first-party text** after deduplication, distributed roughly as (a) ~50k Evans Reference + Vernon aggregate essays + Fowler bliki; (b) ~90k DDD-Crew Markdown wholesale; (c) ~30k awesome-ddd + ADR repos; (d) ~40k Grzybek blog + README; (e) ~60k Verraes + Dahan + Young blogs; (f) ~30k Tune Substack + da Silva + Baasie + Wirfs-Brock free materials; (g) ~80–150k high-value conference transcripts (Brandolini, Evans, Young, Dahan, Verraes, Tune, Khononov, Hjorteland, Baas-Schwegler, Yao, O'Reilly residuality, Wardley, Skelton/Pais). That is feasible for a single retrieval-augmented setup or a Claude/Gemini 1M-context ingestion; for a pure in-context approach, split into **strategic corpus** (Tiers 1+3 strategic) and **tactical corpus** (Tiers 1+2 tactical) and route queries with a classifier.

### License and redistribution notes

CC-BY-SA-4.0 dominates DDD-Crew content — attribution + share-alike required if derivative. CC BY 4.0 on Evans DDD Reference (most permissive). CC BY-ND 3.0 on Vernon *Effective Aggregate Design* — **no derivatives**, which means an AI agent may cite and extract but must not republish modified versions. `heynickc/awesome-ddd` has no explicit LICENSE — attribute only. `VaughnVernon/IDDD_Samples` (Java originals) has no clear OSS licence and should not be redistributed wholesale; the Apache-2.0 C# port (GustinAG) is a safer substitute. `npryce/adr-tools` is GPL for code / CC-BY for docs (mixed). The commercial books must be ingested from legitimately-acquired copies — **do not** pull from unauthorised PDF mirrors (e.g., the Evans manuscript mirror on fabiofumarola.github.io is unauthorised despite returning selectable text).

### Conclusion: what changes once this corpus is loaded

A sparring agent fed this corpus stops being a DDD *explainer* and starts being a DDD *interrogator*. The critical shift is in Tier 3 — **Verraes, Wirfs-Brock, Khononov, Baas-Schwegler, and Hjorteland consistently push back against oversimplified heuristics** (1 BC per subdomain, 1 microservice per BC, SOLID-as-scripture, ubiquitous-language-as-dictionary). Loading those voices alongside the Blue/Red Book canon is what prevents the agent from jumping to premature tactical answers. The user's EV-charging / AFIR / roaming context sits in a published-literature gap; the agent's utility there will come from **disciplined cross-application of insurance-quoting, payment-gateway, and multi-sided-platform patterns** rather than from any direct EV-charging DDD source — which, as of April 2026, does not meaningfully exist and is itself a publishing opportunity.