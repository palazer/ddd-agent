# The Claude Code DDD toolkit in April 2026

**The tooling has matured enough that an enterprise architect can now go from blank-sheet ideation to a Git-versioned, code-generatable domain model entirely inside Claude Code** — but only if you assemble the stack yourself. The single most valuable asset is the `melodic-software/claude-code-plugins` skill bundle (event storming + context mapping + domain storytelling + ADR management), paired with `wshobson/agents` for tactical patterns, and an MCP layer built on the **official Structurizr MCP server**, the **EventCatalog MCP server**, a Mermaid/PlantUML/Kroki renderer, and a ContextMapper CLI wrapped via Bash. The glaring gap — no MCP server wraps ContextMapper or egon.io — is exactly the kind of thing a team-tooling-focused architect can build in a weekend, and it would be the most impactful contribution currently missing from the ecosystem.

Three facts frame the rest of this report. First, most "DDD skills" on the market are either PHP-specific code generators or thin template stubs; the substantive ones can be counted on two hands. Second, Structurizr's CLI and Lite repos were archived in February 2026 and consolidated into a single `structurizr.war`, so greenfield work should target the new tooling. Third, every credible practitioner — Eric Evans at DDD Europe 2025, Michael Plöd, Alberto Brandolini, Martin Fowler — converges on the same message: **LLMs excel at tactical code inside a well-defined bounded context, but strategic discovery and core-domain modeling remain human work**. The tooling should reflect that division of labor.

---

## 1. Claude Code-native assets, ranked

### Tier 1: The four you should install today

**melodic-software/claude-code-plugins** is the only plugin bundle that genuinely covers ideation. Its `event-storming` skill runs a multi-persona workshop (full, quick, and guided modes) and emits an event catalog plus bounded contexts plus hot-spot annotations; the `context-mapping` skill generates ContextMapper CML directly alongside Mermaid views and team-topology mappings; and `domain-storytelling` follows the Hofer/Schwentner methodology. It also ships `adr-management`, `architect-reviewer`, and an `architecture-docs-generator` that produces C4 views from codebases. **This is your primary ideation-to-formalization pipeline** — no other single asset comes close on breadth and depth. Verify licensing per-skill; the parent repo is permissive but not all SKILL.md files declare licenses in frontmatter.

**booklib-ai/booklib** (`skills/domain-driven-design/SKILL.md`, MIT) is the single best standalone DDD SKILL.md. It operates in three modes — code generation, code review, and migration planning — and is grounded explicitly in Evans' Blue Book. The anti-pattern catalog (Anemic Model, God Aggregate, Primitive Obsession, Conformist-when-ACL-needed) plus the five-phase migration plan make it far more useful for review and refactoring than any competing standalone DDD skill. Small repo (~23 stars), actively maintained in 2026.

**wshobson/agents** is the workhorse collection. The `backend-architect` subagent is the best general-purpose architecture agent — it explicitly decomposes services via DDD bounded contexts, covers Saga/CQRS/Outbox, and uses Sonnet model routing. The `architecture-patterns` SKILL adds Hexagonal + DDD tactical building blocks with Python/FastAPI templates. The `architecture-decision-records` SKILL is best-in-class for ADR authoring with full Nygard and MADR templates, lifecycle states, and worked examples. Thousands of stars, very active.

**bitsmuggler/c4-skill** is the best C4/Structurizr skill. It scans a codebase, emits `workspace.dsl`, and exports to PlantUML/Mermaid/PNG via Docker using a bundled `scripts/export-diagrams.sh`. Pairs naturally with the Structurizr MCP server described below.

### Tier 2: Strong supporting assets

**VoltAgent/awesome-claude-code-subagents** contributes a well-structured `architect-reviewer` (formal JSON protocol, 20–30 recommendation roadmap output), a `microservices-architect` that identifies boundaries through DDD, and a `cloud-architect` for Well-Architected work. One caveat: the bundled "voltagent-code-reviewer" marketplace skill was flagged by third-party trust reviewers (Fidensa scored it 52/100) for instruction-volume bloat and prompt-injection risk; **prefer installing individual subagent files from the GitHub repo directly, not the bundled marketplace package**.

**VilnaCRM-Org/structurizr-architecture-sync** keeps `workspace.dsl` in lockstep with the codebase as new CQRS handlers, entities, and repositories appear — useful once you've adopted Structurizr and want the model to stop drifting from reality. It's opinionated toward CQRS/layered codebases with a `deptrac.yaml` alignment assumption.

**dykyi-roman/awesome-claude-code** is a 300+ component PHP-specific DDD toolkit (50+ agents, 200+ skills) with generators for entities, value objects, aggregates, repositories, domain events, specifications, factories, plus a `ddd-auditor` coordinator. Useful if you're on PHP; largely irrelevant otherwise.

**ruvnet/agentic-flow** ships a `v3-ddd-architecture` skill focused on decomposing god-object orchestrators into bounded contexts via microkernel patterns and parallel `Task()` delegation to a `core-architect` subagent. Opinionated to claude-flow's runtime, but the decomposition prompts are adaptable.

**tosin2013/mcp-adr-analysis-server** is technically an MCP (covered below) but worth noting here: it reads your repo's ADR directory, suggests implicit decisions, and provides smart code linking from ADRs to implementing files via tree-sitter AST plus ripgrep.

### What to skip

Several widely-linked "DDD skills" are generic CLAUDE.md snippets, Clean-Architecture style guides dressed up as DDD, or thin templates. Specifically, **treat davila7/claude-code-templates' `software-architecture` skill as a coding-style reference, not a DDD asset** — it's Clean-Architecture-flavored guidance without strategic-design depth. The ETH Zurich AGENTbench study (InfoQ, March 2026) found that LLM-generated context files often *hurt* agent performance on 138 real Python tasks; keep your CLAUDE.md minimal and handwritten, restricted to non-inferable commands and glossary terms. **This is the most important credibility-sourced counter to the current CLAUDE.md cargo cult.**

---

## 2. Adjacent OSS tooling, grouped by DDD phase

### Ideation and discovery

**ddd-crew** (github.com/ddd-crew, CC-BY-SA-4.0) is the canonical set of Markdown canvases — Bounded Context Canvas v3, Domain Message Flow, Aggregate Design Canvas, Core Domain Charts, the DDD Starter Modelling Process — and a new `ai-ddd-prompts-and-rules` repo (June 2025) that ships prompt rules specifically for Cursor and Claude. Claude writes directly into the canvas templates in `docs/ddd/<context>/`. This is the lightest-weight ideation surface and the most LLM-friendly.

**egon.io** (GPL-3.0, WPS, active) is the OSS Domain Storytelling tool. It's a browser app (`docker run ghcr.io/wps/egon.io:latest`) with `.egn` JSON as its on-disk format. Claude can author and edit `.egn` files directly, but **there is no headless renderer** — a human must open the browser to visualize. For most Claude-driven workflows, generating Mermaid or PlantUML sequence diagrams of domain stories is faster than round-tripping through egon.io.

Event storming tooling remains uneven. **Qlerify** (vendor, not OSS) is the most visible AI-native event storming product and recently hosted a Virtual DDD meetup with Eric Evans; the community takeaway was consistent — AI models are a starting point requiring iterative refinement, **generic subdomains work well with AI, core domains require much more human involvement**. There is no OSS Miro-style collaborative event storming canvas that Claude Code can drive headlessly; the gap is currently filled by Claude generating Markdown sticky-note lists via the `melodic-software` event-storming skill.

### Strategic design (bounded contexts, context maps)

**ContextMapper** (github.com/ContextMapper, Apache 2.0, v6.12.0, active) is the most LLM-friendly strategic-DDD tool in existence. CML is a deterministic Xtext grammar covering Bounded Contexts, Aggregates, Entities, Value Objects, Relationship patterns (OHS/PL, ACL, Shared Kernel, Customer-Supplier, Conformist), Stakeholders, and Value Registers. The `context-mapper-cli` ships as `org.contextmapper:context-mapper-cli:6.12.0` and supports `cm validate -i model.cml` and `cm generate -g plantuml|context-map|mdsl|generic`. Architectural Refactorings (AR-1 through AR-11: Split Aggregate by Entities, Extract Shared Kernel, Merge Bounded Contexts, etc.) are invokable from CLI. **This should be the spine of your formalization pipeline.**

**Structurizr** (Apache 2.0, in transition). The CLI and Lite repos were **archived in February 2026**; the replacement is a single Spring Boot `structurizr.war` in `github.com/structurizr/structurizr`. The DSL itself is stable and unchanged. The cloud service is announced EOL on 1 July 2026 (read-only thereafter). Prebuilt server binaries now require a commercial license but the source-build path remains fully OSS. **Target the new `structurizr.war` for greenfield work**; the legacy `structurizr.sh` still functions for existing pipelines.

**MDSL** (Microservice-API-Patterns/MDSL-Specification, Apache 2.0) bridges CML to OpenAPI/Protobuf/GraphQL/Jolie/Java skeletons via `mdsl -i contracts/customer.mdsl -g oas -o generated/`. Flagged: Olaf Zimmermann's group has publicly stated MDSL is a "technology demonstrator" with sparse maintenance resources; the core grammar is stable but flow/story features are experimental. Use as an optional bridge, not a core dependency.

### Tactical design in code

**JMolecules** (xmolecules/jmolecules, Apache 2.0, 1.4k stars, active) is the best way to express DDD building blocks in Java/Kotlin code without a framework lock-in. Annotations like `@AggregateRoot`, `@ValueObject`, `@DomainEvent`, `@Repository`, and `@DomainLayer` are enforced at build time by ArchUnit rules (`JMoleculesDddRules.all()`, `JMoleculesArchitectureRules.ensureOnionSimple()`) and translated to JPA/Spring via a ByteBuddy plugin. Claude can mechanically apply annotations, then `mvn test` verifies. **kMolecules** extends this to Kotlin.

**Sculptor** is effectively abandoned — last tagged release was 3.1.0 in April 2015, depends on Eclipse/Xtext/Xtend. The user's mention of "JDDD" appears to be a confusion with jMolecules; no distinct "JDDD" OSS project exists. **Use jMolecules plus Spring Modulith for modern Java DDD.**

### Contracts between bounded contexts

**AsyncAPI tooling** (Apache 2.0, very active) is the strongest event-contract formalization path. `asyncapi validate`, `asyncapi convert` (to/from OpenAPI), `asyncapi diff -t breaking`, `asyncapi bundle`, and `asyncapi generate fromTemplate` cover the full documentation and code-generation surface. Generator v3 is now exclusive; Node 24+ is required post-Shai-Hulud npm hardening.

**OpenAPI** (various Apache-2.0 implementations) pairs with AsyncAPI for REST surfaces and integrates cleanly via ContextMapper → MDSL → OpenAPI.

### Rendering

**Kroki** (yuzutech, MIT, active) is the single best one-stop diagram renderer for an LLM workflow — one HTTP endpoint or one CLI renders 25+ formats including PlantUML, Mermaid, Structurizr DSL, C4, BPMN, D2, Graphviz, Excalidraw, ERD, DBML. `docker run -d -p 8000:8000 yuzutech/kroki` then POST any diagram source with a `diagram_type`. Deploy this once and every downstream render becomes a trivial shell call.

**C4-PlantUML** (plantuml-stdlib, MIT, active — regular 2025 releases) remains the highest-fidelity C4 renderer. The new `!NEW_C4_STYLE=1` and `!ROUNDED_STYLE=1` flags and theme fixes for PlantUML 1.2025.1 ship with full macro coverage (`Person`, `System`, `Container`, `Component`, `Rel`, `Boundary`, `Deployment_Node`).

**Mermaid C4** is still flagged experimental in Mermaid's own docs; layout quality lags C4-PlantUML significantly on Component views. Good enough for Context and Container diagrams rendered inline in GitHub READMEs.

### Documentation

**EventCatalog** (github.com/event-catalog/eventcatalog, mixed OSS/commercial, 2.7k stars, very active — last update 9 April 2026) is the de-facto event-driven architecture documentation tool. Domains/services/events/commands/queries/flows/entities/teams are authored as MDX + frontmatter markdown, generators pull from AsyncAPI/OpenAPI/Kafka/Confluent/AWS EventBridge, and it ships a built-in MCP server plus a dedicated flow-wizard skill (`npx skills add event-catalog/skills --skill flow-wizard`) that installs into `.claude/skills/`. Some features (OAuth2, RBAC, schema governance, breaking-change detection) require the Scale/Enterprise license.

**adr-tools** (npryce, MIT), **log4brains** (thomvaill, Apache-2.0, 1.4k stars, MADR-compatible), and the **MADR template** (adr/madr, CC0) cover decision records. The old `adr/adr-tools` MADR fork is **archived** — use log4brains or plain adr-tools with the MADR template. Both are trivial Bash orchestration targets for Claude.

### Enterprise architecture

**Archi** (archimatetool, MIT for the tool itself) handles ArchiMate modeling. The Eclipse-RCP-based ACLI requires `xvfb-run` on headless Linux, and **jArchi scripting is commercial-licensed for commercial use** — only the base tool is fully free. Overkill for most DDD work; only reach for it if you need formal ArchiMate traceability for enterprise governance.

---

## 3. MCP servers: what exists, what's missing

### Architecture-as-code MCPs

**Structurizr MCP server (official)** is the most important MCP in this space. It runs at `mcp.structurizr.com/mcp` as a hosted endpoint with DSL, PlantUML, and Mermaid tools enabled, ships as `docker pull structurizr/mcp`, and is buildable from source in `structurizr/structurizr`. Exposes tools for workspace inspection, DSL parsing/validation, and DSL update with layout merging. **This is the reference implementation and what you should target.**

**LikeC4** (likec4.dev, active) ships both Agent Skills (auto-loaded when editing `.c4`/`.likec4` files via the Agent Skills Discovery protocol) and an MCP server exposing `list-projects`, `read-project-summary`, `search-element`, `read-element`, and `read-view`. LikeC4's DSL is more flexible than Structurizr but diverges from Simon Brown's canonical C4; choose it when you need programmatic agent-queryable models and don't require official C4 lineage.

**Cubical6/structurizr-mcp** is a PHP community MCP with 23 tools for C4 workspace management (create/list/delete workspaces, add people/systems/containers/components, define relationships, generate views, export DSL). Useful if you want the agent to build Structurizr models from scratch rather than editing existing DSL. Lower stars, newer project — vet before depending on it.

**psousa50/c4-structurizr-mcp-server** is a focused validation and formatting MCP for Structurizr DSL — useful as a guardrail in CI to catch agent-authored DSL mistakes before they land.

### Diagram rendering MCPs

**veelenga/claude-mermaid** is the strongest Mermaid MCP for Claude Code: live browser reload, SVG/PNG/PDF export, multiple themes, multi-preview support. Installs via `claude-mermaid` npm package, auto-configured as a plugin.

**ByteOverDev/diagrams-mcp** wraps three rendering engines (mingrammer/diagrams, Mermaid, PlantUML) behind one MCP with a public hosted endpoint at `diagrams-mcp-production.up.railway.app/mcp`. `uvx diagrams-mcp-server` for local use.

**antoinebou12/uml-mcp** supports 30+ diagram types via Kroki including **Structurizr**, C4, BPMN, and D2 — a pragmatic single-dependency option that covers almost everything rendering-related. Also has a hosted endpoint at `uml-mcp.vercel.app/mcp`.

**tkoba1974/mcp-kroki**, **hustcc/mcp-mermaid**, and **peng-shawn/mermaid-mcp-server** are viable alternatives with slightly different feature emphases (Kroki API-gateway pattern, file-output support, SSE/streamable HTTP transports).

### Event-driven and catalog MCPs

**event-catalog/mcp-server** (official, TypeScript) and the related **surgeventures/event-catalog-mcp-server** fork expose 15 tools for querying domains, services, events, schemas, owners, producers, consumers, and flows. The flagship workflow is "Here is a new version of the UserCreated schema — which downstream consumers will break?" and the server answers authoritatively from your catalog. As of EventCatalog 2.64.0, a **built-in MCP server** is shipped inline with the instance (requires SSR mode and Scale license); the standalone `@eventcatalog/mcp-server` package still works and is being soft-deprecated. There is also a flow-wizard skill that pairs with the MCP server to interactively document business flows.

### Decision-record MCPs

**tosin2013/mcp-adr-analysis-server** (MIT, 73 tools) analyzes ADRs with tree-sitter AST parsing, generates ADRs from PRDs, and implements smart code linking from decisions to implementing files using ripgrep plus AI-powered keyword extraction. OpenRouter.ai integration required for the AI features. This is the most substantive ADR MCP in the ecosystem.

**wooxogh/adr-mcp-setup** auto-captures Claude Code conversations via a Stop Hook, uses Claude Opus to extract ADR content, scores completeness 0–100, detects duplicates against past ADRs, and maintains a dependency graph. Very aligned with the "ADR as append-only log" discipline.

### The gaps, which are directly actionable

**No MCP server wraps ContextMapper.** A Bash/stdio MCP exposing `cm validate`, `cm generate -g plantuml|context-map|mdsl|generic`, and the eleven Architectural Refactorings would be the single most valuable contribution the user could make. It's a thin wrapper over an existing Java CLI and would unlock fully agent-driven strategic modeling.

**No MCP for MDSL, Archi, or egon.io.** MDSL and egon.io both ship CLIs or Docker containers that would be easy to wrap. Archi is harder because of its Eclipse-RCP dependency, but an MCP that manipulates the Open Group Exchange XML directly would sidestep that.

**No "event storming facilitator" MCP.** The melodic-software event-storming skill is excellent at running a workshop inside a conversation, but a stateful MCP that maintains a running event storming canvas as structured data across sessions (and renders to PNG via Kroki on demand) doesn't exist. Real opportunity.

**No MCP for the ddd-crew canvases.** A trivial MCP exposing "create bounded context canvas", "update domain message flow", "validate aggregate design canvas" would make the canvases first-class agent surfaces rather than filesystem-edited Markdown.

---

## 4. A recommended end-to-end workflow

The stack below moves from blank-sheet ideation to a Git-versioned, code-generatable domain model. Every step is a bash command Claude Code can orchestrate, every artifact is text-diffable, and every human checkpoint is explicit.

**Phase 1 — Blank-sheet ideation (human-led, AI-assisted).** Run the `melodic-software` event-storming skill in Claude Code to facilitate a structured workshop. Use the multi-persona mode for breadth, guided mode when you have a specific subdomain in mind. Output is a Markdown event catalog plus candidate bounded contexts plus hot-spot annotations written to `docs/ddd/event-storming/session-<date>.md`. **Human checkpoint: review the event catalog before accepting any context boundaries.** This is where Plöd's L3 ceiling applies — for Core + Complex domains, AI is a sparring partner, not a decider.

**Phase 2 — Strategic design.** Instruct Claude to populate `docs/ddd/<context>/bounded-context-canvas.md` (from `ddd-crew`) for each candidate context, then promote stabilized canvases into a ContextMapper CML model at `src/main/cml/<system>.cml`. Claude authors CML directly because the Xtext grammar is deterministic and LLM-friendly. Run `java -jar context-mapper-cli-6.12.0.jar validate -i src/main/cml/<system>.cml` as a gate. Iterate using the Architectural Refactorings: "apply AR-1 Split Aggregate by Entities", "apply AR-7 Merge Bounded Contexts" — Claude invokes these through the CLI. Render context maps via `cm generate -g context-map` and PlantUML views via `cm generate -g plantuml`.

**Phase 3 — Contracts between contexts.** Generate MDSL from CML (`cm generate -g mdsl`) or hand-write AsyncAPI/OpenAPI YAML. Validate via `asyncapi validate spec/orders.yaml` and `asyncapi diff old.yaml new.yaml -t breaking` before landing any change. Use the EventCatalog MCP server to ask "which downstream consumers consume `OrderPlaced` v2?" before proposing breaking schema changes.

**Phase 4 — Tactical design.** Claude scaffolds Java or Kotlin classes annotated with `@AggregateRoot`, `@Entity`, `@ValueObject`, `@DomainEvent` from jMolecules, aligned to the CML aggregate definitions. ArchUnit rules (`JMoleculesDddRules.all()`) enforce consistency on every `mvn test`. **Human checkpoint: invariants.** Write property tests or Gherkin specs describing aggregate invariants *before* letting the agent implement methods — this is the "executable specifications as external anchors" pattern from the February 2026 arXiv correlated-errors paper.

**Phase 5 — Documentation and decisions.** ADRs via `adr new <title>` or `log4brains adr new`, with Claude populating the MADR body. Use `wshobson/agents` ADR skill for template quality, and the `tosin2013/mcp-adr-analysis-server` MCP for smart code linking. Publish an EventCatalog instance aggregating AsyncAPI/OpenAPI plus markdown, with the EventCatalog MCP server connected for live agent queries.

**Phase 6 — Rendering.** Run a single `yuzutech/kroki` container. Every PlantUML, Mermaid, Structurizr DSL, C4, or BPMN source gets rendered to SVG/PNG via one HTTP endpoint. Commit the SVGs next to the source. Use `bitsmuggler/c4-skill` to regenerate Structurizr views from the codebase periodically as a drift check.

**Seven human-in-the-loop gates** emerge from the combined practitioner advice: (1) event storming kickoff is human-led; (2) bounded-context boundaries require human acceptance; (3) aggregate invariants are human-written as tests first; (4) cross-context changes get mandatory PR review because agents leak abstractions; (5) any core-domain modeling operates at Plöd's L3 "sparring" ceiling, never L4 autonomous; (6) the "two failed corrections" rule — if the agent can't fix something after two tries, `/clear` and rewrite the prompt; (7) a weekly quality review where one AI-introduced issue per developer gets fixed, to counter the 6-month cognitive-complexity creep documented in the Code Rabbit and Cortex data.

---

## 5. Gaps and opportunities actionable by your team

The highest-leverage tooling gap in April 2026 is **a ContextMapper MCP server**. CML is the most LLM-friendly DDD DSL in existence, the CLI is Apache-2.0 and stable at 6.12.0, and wrapping `cm validate`, `cm generate`, and the eleven Architectural Refactorings as MCP tools is a two-day project that would instantly become the most important piece of the DDD agent stack. A corresponding **"CML authoring skill"** published as a SKILL.md would teach Claude the grammar and common idioms, closing the loop.

The second gap is **a stateful event storming MCP**. The melodic-software skill runs excellent conversational workshops but loses state on session end. A small server maintaining a running event storming canvas (events, commands, actors, policies, read models, hot spots) as structured JSON, with tools for "add event", "identify aggregate", "mark pivotal event", and rendering to Miro-compatible JSON or PlantUML, would be a genuinely novel capability and would position the owner as the leading LLM + event storming tooling author in a field currently dominated by the commercial Qlerify.

Third, **nobody has published a serious "strategic-DDD agent"** — a subagent specifically tuned for the Nick Tune / Michael Plöd strategic modeling process (domains → subdomains → core/supporting/generic classification → team topologies → context map with relationship patterns). The Plöd 4-level delegation framework mapped onto Cynefin is the rigorous public framework for this, but no corresponding `.claude/agents/strategic-ddd-architect.md` implementation exists. The `backend-architect` from wshobson is the closest substitute and is tactical-leaning.

Fourth, **the CLAUDE.md patterns published publicly for DDD work are mostly cargo-culted**. The ETH Zurich AGENTbench paper (InfoQ, March 2026) showed LLM-generated context files measurably hurt success rates on real tasks. A well-researched, handwritten CLAUDE.md template for domain-modeling projects — glossary slots, invariant slots, context-boundary declarations, aggregate root catalog, event naming conventions, explicit "this is the language, do not invent synonyms" guardrails — would be high-leverage and trivially publishable. Bardia Khosravi's "Backend Coding AI Context" on Medium is the current closest reference but stops short of a domain-aware template.

Fifth, **Structurizr's transition from CLI/Lite to the consolidated `structurizr.war`** is under-documented in the agent-tooling space. Existing skills and MCPs target the legacy tooling; an update pass is in order. The EOL of the Structurizr cloud on 1 July 2026 (read-only thereafter) will force teams off the hosted service — a **"structurizr-migrate" skill** that takes an exported cloud workspace and lays it out correctly for the new WAR-based deployment would have a well-defined audience window.

## Conclusion

The stack to beat today is **melodic-software plugins for ideation, booklib DDD skill for tactical review, ContextMapper CLI as the strategic spine, jMolecules for in-code expression, EventCatalog (with its MCP) for event documentation, and Kroki plus C4-PlantUML for rendering** — orchestrated from Claude Code with the official Structurizr MCP and the EventCatalog MCP connected. The specific missing piece that would tip the balance from "capable but assembled" to "coherent platform" is a ContextMapper MCP, which is a directly buildable project for an enterprise architect producing team tooling. The practitioner consensus from Evans, Plöd, Brandolini, Fowler, and Böckeler is consistent and worth internalizing before writing a single CLAUDE.md line: **LLMs produce tactical code fluently inside well-defined bounded contexts, but bounded-context discovery and core-domain modeling remain human work, with the agent acting as a sparring partner at best**. Build your tooling to reinforce that division, not to paper over it.