# Phase 0 — OSS Audit & Attribution Ledger

Audit of cloned upstream repositories under `vendor/` to decide what we vendor, what we rewrite, and what we reject for v1. Also the canonical attribution ledger for anything we copy into `kb/` or `.claude/`.

All audits performed 2026-04-19.

---

## Summary of licensing

| Upstream | License | Share-alike obligation? | v1 distribution plan |
|---|---|---|---|
| `melodic-software/claude-code-plugins` | MIT | No | Vendor & modify freely with copyright notice |
| `ddd-crew/bounded-context-canvas` | CC BY 4.0 | **No** (not SA) | Vendor content with attribution; we can modify |
| `ddd-crew/aggregate-design-canvas` | CC BY 4.0 | **No** | Same |
| `ddd-crew/core-domain-charts` | CC BY 4.0 | **No** | Same |
| `ddd-crew/ddd-starter-modelling-process` | CC BY 4.0 | **No** | Same |
| `ddd-crew/ai-ddd-prompts-and-rules` | not declared (repo default) | Treat as fair-use reference | Cite URL only |
| `wshobson/agents` | not inspected for v1 | Unknown | Defer; not v1 scope |

**Correction from plan:** ddd-crew repos are **CC BY 4.0**, not CC BY-SA 4.0 as Doc 2 claimed. No share-alike obligation. We can vendor verbatim or modified with attribution.

---

## Asset-by-asset decisions

### 1. `melodic-software/claude-code-plugins` (MIT)

Doc 2's claim about this repo was correct — I initially missed it because the DDD skills live under `plugins/*/skills/` not `.claude/skills/`. The DDD-relevant material is rich.

| File | Nature | Decision |
|---|---|---|
| `plugins/enterprise-architecture/skills/event-storming/SKILL.md` | AI-simulated Event Storming with 5-persona multi-agent mode + 6 phases | **Vendor & adapt** → `.claude/skills/event-storming/SKILL.md`. Rewrite to cite `kb/ceremonies/event-storming.md`, enforce Plöd-L3 checkpoints, emit to `projects/<slug>/event-storm.md`. Keep persona structure. |
| `plugins/enterprise-architecture/skills/event-storming/references/workshop-facilitation.md` | 6-phase timing guide (40% chaotic, 15% timeline, 15% commands, 10% actors, 15% BC discovery, 5% hot spots) | **Vendor verbatim** → `.claude/skills/event-storming/references/workshop-facilitation.md`. Gold-standard process content. |
| `plugins/enterprise-architecture/skills/event-storming/references/persona-prompts.md` | Persona prompt templates | **Vendor verbatim** → same dir. |
| `plugins/enterprise-architecture/skills/event-storming/references/sticky-note-types.md` | Brandolini color conventions | **Vendor verbatim** → same dir. |
| `plugins/enterprise-architecture/skills/event-storming/references/bounded-context-discovery.md` | BC-discovery heuristics | **Vendor verbatim** → same dir. |
| `plugins/enterprise-architecture/skills/event-storming/references/templates/event-storm-output.md` | Output format template | **Vendor verbatim** → same dir. |
| `plugins/enterprise-architecture/agents/event-storming-persona.md` | 5-persona agent (business-analyst, developer, devils-advocate, domain-expert, product-owner) with `event-storming-personas.yaml` config | **Vendor & adapt** → `.claude/agents/event-storming-persona.md`. Keep the 5-persona structure — it's the multi-perspective lens the Plöd-L3 interrogator needs. |
| `plugins/enterprise-architecture/agents/context-mapper.md` | Context Mapping pattern recognition with Shared-Kernel/Partnership/Customer-Supplier/Conformist/ACL/OHS decision tree + CML DSL generation + team-topology alignment | **Vendor & adapt** → `.claude/agents/context-mapper.md`. Drop the CML-specific generation for v1 (we're not running ContextMapper yet); keep the pattern-recognition decision tree and relationship table. |
| `plugins/enterprise-architecture/agents/story-facilitator.md` | Domain Storytelling facilitator (Hofer/Schwentner method): actors, work objects, AS-IS/TO-BE | **Vendor & adapt** → `.claude/agents/story-facilitator.md`. Already aligned with our ceremony. |
| `plugins/spec-driven-development/agents/adr-creator.md` | MADR-format ADR writer with alternative extraction | **Vendor & adapt** → `.claude/agents/adr-creator.md`. Strip spec-driven-development coupling; wire to `projects/<slug>/adr/`. |
| `plugins/event-modeling/agents/event-modeler.md` | Adam Dymitruk's visual Event Modeling methodology (swimlanes, commands, events, read models, slices) | **Defer to v2**. This is more tactical / implementation-oriented than ideation. |
| `plugins/event-modeling/skills/event-storming-advanced/` | Deep-dive Event Storming (process-level, design-level, software design mapping) | **Defer to v2**. Big-Picture only for v1. |
| `plugins/event-modeling/skills/{event-sourcing-design,cqrs-architecture,saga-patterns}/` | Tactical/integration patterns | **Defer to v2** — out of ideation scope. |
| `plugins/enterprise-architecture/agents/{over-engineering-detector, principles-validator}` | Anti-pattern / principles challengers | **Reference only** — inspire but don't vendor; we write our own interrogator with DDD-specific anti-patterns. |
| `plugins/business-analysis/skills/{business-model-canvas, journey-mapping, capability-mapping, stakeholder-analysis}` | Business-analysis ceremonies | **Defer** — adjacent to ideation but v2 scope expansion. |
| `plugins/requirements-elicitation/*` | Interview, gap-detection, journey-mapping | **Defer**. |

**What we reject outright:** Dometrain scraped course content, Windows edit-bug workarounds, markdown-linting infrastructure, google/openai/gemini ecosystem agents — all irrelevant to DDD ideation.

**Attribution to include in every vendored file's frontmatter:**
```
vendored_from: melodic-software/claude-code-plugins
license: MIT
upstream_path: plugins/enterprise-architecture/skills/event-storming/SKILL.md
ingested: 2026-04-19
adaptation_notes: <brief note on what we changed>
```

---

### 2. `ddd-crew/bounded-context-canvas` (CC BY 4.0)

| File | Nature | Decision |
|---|---|---|
| `README.md` | Canonical Bounded Context Canvas v5 section definitions: Name, Purpose, Strategic Classification, Domain Roles, Inbound/Outbound Communication, Ubiquitous Language, Business Decisions, Assumptions, Verification Metrics, Open Questions | **Vendor verbatim** → `kb/ceremonies/bounded-context-canvas.md` with attribution frontmatter. Authoritative template text. |
| `resources/model-traits-worksheet.md` | Michael Plöd's bounded-context archetype worksheet (Specification, Execution, Analysis, Approver, Enforcer, Gateway, Bubble, Brain, Funnel, Engagement contexts) | **Vendor verbatim** → `kb/patterns/bounded-context-archetypes.md`. Gold. |
| Image resources (jpg, svg, excalidraw) | Visual canvas templates | **Skip** — we're building a text-driven agent; keep originals in `vendor/` for reference. |

**Upstream contributors to credit:** Kenny Baas, Kim Lindhard, Michael Plöd, Maxime Sanglan-Charlier; inspired by the Business Model Canvas.

---

### 3. `ddd-crew/aggregate-design-canvas` (CC BY 4.0)

| File | Nature | Decision |
|---|---|---|
| `README.md` | Aggregate Design Canvas v1.1 with 9 sections (Name, Description, State Transitions, Enforced Invariants, Corrective Policies, Handled Commands, Created Events, Throughput, Size) + concurrency-conflict evaluation chart + size evaluation chart | **Vendor verbatim** → `kb/patterns/aggregate-design-canvas.md`. Even though v1 stops short of full tactical, the canvas itself is an excellent "thinking" artifact and fair game for ideation-phase sparring. |
| Image charts | Visual aggregate/concurrency charts | Skip; keep in `vendor/`. |

**Upstream contributors:** Mathew McLoughlin, Marijn Huizendveld; inspired by the Bounded Context Canvas.

---

### 4. `ddd-crew/core-domain-charts` (CC BY 4.0)

| File | Nature | Decision |
|---|---|---|
| `README.md` | Core Domain Chart usage guide with complexity-vs-differentiation questions, architecture-migration variant, Team-Topologies interaction-mode augmentation | **Vendor verbatim** → `kb/patterns/core-domain-chart.md`. |
| `examples/first-to-market-core.md`, `examples/pioneers-run-experiments.md` | Worked examples | **Vendor** → `kb/patterns/core-domain-chart-examples.md` (combined). Good material for interrogator to cite. |

**Upstream contributors:** Eduardo da Silva (primarily).

---

### 5. `ddd-crew/ddd-starter-modelling-process` (CC BY 4.0)

| File | Nature | Decision |
|---|---|---|
| `README.md` | 8-step iterative process: Understand → Discover → Decompose → Strategize → Connect → Organise → Define → Code, with tools and who-to-involve per step | **Vendor verbatim** → `kb/patterns/ddd-starter-modelling-process.md`. This is the **master backbone of our `/ddd:ideate` orchestrator**. Our 7-step flow is a subset (stopping before Code). |
| Image resources | Process diagrams | Skip; keep in `vendor/`. |

**Upstream contributors:** Ciaran McNulty, Eduardo da Silva, Gien Verschatse, James Morcom, Maxime Sanglan-Charlier.

---

### 6. `ddd-crew/ai-ddd-prompts-and-rules` (license not declared)

| File | Nature | Decision |
|---|---|---|
| `README.md` + `rules/README.md` + `prompts/README.md` | Meta-guidance on using AI rules files (Cursor, Copilot, Junie) | **Reference only** — not content-rich. |
| `rules/domain-terminology-mismatch/README.md` + `.mdc` | Rule file encouraging the agent to detect and propose refactoring for domain-term drift (e.g., "Pineapple" → "Ananas") | **Concept-vendor only** — fold the *idea* (terminology-drift detection) into `.claude/agents/glossary-curator.md` behavior. Don't copy the file — license unclear. |

---

### 7. `wshobson/agents`

**Decision:** **Defer audit** — outside v1 ideation scope. Per Doc 2, its strongest agents (`backend-architect`, `architecture-patterns`) are tactical-leaning. Revisit in v2 when we add tactical skills.

---

## Deferred to v2+ (per original plan; confirmed here)

- `booklib-ai/booklib` — tactical DDD review + codegen
- `bitsmuggler/c4-skill` — C4 diagrams
- `tosin2013/mcp-adr-analysis-server` — ADR MCP (useful once we have a codebase)

---

## Net v1 "borrow vs build" picture

**Vendored & adapted from melodic-software (MIT):**
- 1 skill (`event-storming`) + 5 reference files
- 4 agents (`event-storming-persona`, `context-mapper`, `story-facilitator`, `adr-creator`)

**Vendored verbatim from ddd-crew (CC BY 4.0):**
- 5 `kb/` files (bounded-context-canvas, aggregate-design-canvas, core-domain-chart, core-domain-chart-examples, ddd-starter-modelling-process, bounded-context-archetypes)

**Written native (no strong upstream):**
- 4 skills: `cynefin-framing`, `example-mapping`, `ubiquitous-language`, `bounded-context-canvas` (our filler skill; the canvas itself is vendored)
- 2 agents: `ddd-interrogator` (the Plöd-L3 push-back persona), `glossary-curator` (term-drift + synonym detection, inspired by ai-ddd-prompts-and-rules)
- 1 orchestrator agent: `ddd-facilitator`
- 6 slash commands
- `kb/canonical/*`, `kb/voices/*`, `kb/anti-patterns/*` (condensed summaries of Evans/Vernon/Fowler/Verraes/Tune/Khononov etc.)

**Reduction in build effort:** ~40% of the skill/agent writing becomes diff-and-customize; the canvases are ready-made.
