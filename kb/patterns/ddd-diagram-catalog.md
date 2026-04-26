---
title: DDD Diagram Catalog (4-Tier)
source: synthesis of Tune (Domain-Driven Architecture Diagrams), Brandolini (EventStorming), Khononov (Learning DDD), Vernon (Implementing DDD), ddd-crew (Core Domain Chart, BC Canvas), Simon Brown (C4 Model), Hofer/Schwentner (Domain Storytelling)
author: native (ddd-agent project, derived from charge-relay empirical evidence)
license: CC BY-SA 4.0
tier: 1
tags: [diagrams, visualization, mermaid, c4, eventstorming, ddd-diagrams]
ingested: 2026-04-25
vendored_from: native
adaptation_notes: First-class reference for `/ddd:diagrams`. Curates which diagrams every DDD project should produce (Tier 1), which become valuable as the project matures (Tier 2-4), and which are domain-specific narratives that should NEVER be auto-suggested (out-of-scope).
---

# DDD Diagram Catalog

A 4-tier curation of which diagrams a DDD project should produce, when, and
why. Drives the `/ddd:diagrams` skill.

## Why a tier system at all

Every DDD project benefits from a small set of universal diagrams (capability
map, context map, aggregate model, event ribbon, state machines). Beyond that,
diagrams are valuable only when specific signals appear — the actor-context
matrix is wasted effort if there are only 3 actors; the ADR dependency graph
adds nothing with 5 ADRs but becomes essential at 25.

The literature is consistent that *too many diagrams is worse than too few* —
"I only recommend creating diagrams if they give you an advantage" (Tune,
*Domain-Driven Architecture Diagrams*). The tiers below operationalise that
advice into concrete trigger conditions.

## The 4 tiers

### Tier 1 — Universal essentials (5 diagrams)

Always offered by `/ddd:diagrams`. These are the diagrams the canonical DDD
literature converges on as foundational.

| ID | Diagram | Required artifacts | Canonical source |
|---|---|---|---|
| **T1.1** | Domain Capability Map | `5-bc-candidates.md`, `6-contexts/` | Khononov, *Learning DDD* (subdomain decomposition); ddd-crew Core Domain Chart |
| **T1.2** | Bounded Context Map | `7-context-map.md` | Evans, *DDD* ch. 14; Brandolini; Tune #2 |
| **T1.3** | Aggregate Conceptual Model | `4-glossary.md`, `6-contexts/` | Vernon, *IDDD* ch. 10; Tune #5 (Domain Concept Diagram) |
| **T1.4** | Big-Picture Event Ribbon | `2-event-storm.md` | Brandolini, *EventStorming*; pivotal events overlay |
| **T1.5** | State Machines (per stateful aggregate) | `4-glossary.md` (state-bearing entries) | Vernon, *IDDD* ch. 6; Evans on state lifecycles |

**Skip rules** (must be explicit in the recommendation summary):
- T1.2 — skip if the project has only 1 BC. A "context map" of one box is
  noise.
- T1.4 — skip if `2-event-storm.md` does not exist. The skill will not
  invent events.
- T1.5 — skip if no state-bearing entries are detected in the glossary. A
  state-bearing entry is one with an explicit list of states (e.g.
  `States: PROPOSED → ACTIVE → SETTLED`).

### Tier 2 — Strategic frame (3 diagrams)

Offered when the project shows strategic depth: a Cynefin framing of
Complex/Core, a meaningful actor count, or external integration concerns.

| ID | Diagram | Trigger | Canonical source |
|---|---|---|---|
| **T2.1** | Strategic Classification Heatmap | Project has Core Domain Chart filled (or BCs classified Core/Supporting/Generic) | ddd-crew Core Domain Chart (`kb/patterns/core-domain-chart.md`) |
| **T2.2** | Actor-Context Interaction Matrix | ≥ 5 distinct actors detected in `2-event-storm.md` and ≥ 2 BCs | Tune #4 (Business Use Case, matrix variant) |
| **T2.3** | External Systems Landscape (C4 System Context) | ≥ 3 external systems detected in `2-event-storm.md` (pink stickies) | Brown, *C4 Model* (Level 1: System Context); Tune #1 |

### Tier 3 — Behavior & integration (3 diagrams)

Offered when the project has detailed behavioural prose: golden-path flows,
ADR-recorded fallback decisions, or multi-party authorisation rules.

| ID | Diagram | Trigger | Canonical source |
|---|---|---|---|
| **T3.1** | Golden-path Sequence (one per pivotal-event chain) | Pivotal events identified in `2-event-storm.md`; user picks which chains to visualise | Tune #4 (Business Use Case sequence); Brandolini pivotal events |
| **T3.2** | Failure / Fallback Decision Tree | An ADR captures a degradation/fallback policy (keyword scan: "fallback", "degraded", "timeout", "graceful") | (universal pattern; not a single canonical source) |
| **T3.3** | Authorization / Visibility Matrix | ≥ 3 distinct party roles in glossary; multiple BCs with role-scoped read access | Khononov (governance lens, `kb/voices/khononov-pragmatic-strategic.md`) |

### Tier 4 — Reference & governance (3 diagrams)

Offered when the project is mature enough that meta-diagrams about the
artifact set itself become useful.

| ID | Diagram | Trigger | Canonical source |
|---|---|---|---|
| **T4.1** | Coupling & Volatility Matrix | ≥ 4 BCs and Khononov-cited prose anywhere | Khononov, *Learning DDD* ch. 8 (`kb/voices/khononov-pragmatic-strategic.md`) |
| **T4.2** | ADR Dependency Graph | ≥ 10 ADRs in `7-adr/` | (process artifact; MADR ecosystem) |
| **T4.3** | Domain Storytelling Pictographs (Mermaid approximation) | `3-domain-stories.md` exists | Hofer & Schwentner, *Domain Storytelling*; Mermaid is an approximation only — true pictographs need Excalidraw |

### Mermaid type per diagram (locked)

Each diagram ID is rendered with a fixed Mermaid type. The skill's templates
(`.claude/skills/diagram-catalog/templates/<TIER-ID>.md.tmpl`) emit these
exact types — agents and contributors should not silently substitute another
diagram type without amending this table and the corresponding template.

| ID | Mermaid type | Notes |
|---|---|---|
| **T1.1** | `flowchart TB` | Subgraphs per strategic class (Core / Supporting / H2) |
| **T1.2** | `flowchart LR` | Edges labelled with integration pattern + volatility emoji |
| **T1.3** | `classDiagram` | Composition `*--` for entities; `-->` for ID references |
| **T1.4** | `timeline` | Sections per phase; pivotal events tape-marked with 🔖 |
| **T1.5** | `stateDiagram-v2` | One block per stateful aggregate |
| **T2.1** | `quadrantChart` | Plots BCs on Differentiation × Strategic Importance |
| **T2.2** | Markdown table + `flowchart LR` | Hybrid: table is canonical; companion flowchart shows only `RW` (high-touch) edges |
| **T2.3** | `C4Context` | Native C4 Level 1 syntax (`Person`, `System`, `System_Ext`, `Rel`) |
| **T3.1** | `sequenceDiagram` | One file per pivotal-event chain; `autonumber` on |
| **T3.2** | `flowchart TD` | Diamond decision nodes; coloured terminal-outcome leaves |
| **T3.3** | Markdown table + `flowchart LR` | Hybrid: table is canonical; companion flowchart shows only `RW` grants |
| **T4.1** | Markdown table + `flowchart LR` | Hybrid: NxN BC matrix + companion graph for high-coupling pairs |
| **T4.2** | `flowchart TD` | ADR nodes coloured by status; `supersedes` edges dashed |
| **T4.3** | `flowchart LR` | Approximation of Hofer/Schwentner pictographs; emoji glyphs + classDef colours |

The hybrid templates (T2.2, T3.3, T4.1) emit a markdown table as the
canonical artifact and a Mermaid block alongside it — Mermaid has no native
matrix node, and the table is the natural primitive for matrix-shaped
information. Honest about limitations rather than forcing a Mermaid layout
that obscures the data.

### Out of scope — case-specific narrative diagrams

The `/ddd:diagrams` skill explicitly **refuses** to auto-generate the
following, because they are domain-specific narratives that don't generalise:

- Multi-party payment / settlement flows (e.g. charge-relay D8). Generalisation
  attempted in T3.1 (golden-path sequence) is sufficient.
- Layered validation stacks (e.g. charge-relay D12 PAR stack). Domain-specific
  per integration partner; better captured as prose in a BC canvas.
- Targeting / predicate resolution pipelines (e.g. charge-relay D14). The
  underlying decision logic belongs in an ADR, not a flow diagram.

If a user explicitly asks for one of these, the skill emits an empty stub
file with a one-paragraph "structure your own" note pointing at the user's
existing prose artifacts.

## What "produces" a diagram

The `/ddd:diagrams` skill emits **Mermaid markdown source** (`<NN>-<slug>.md`)
into `projects/<slug>/0-diagrams/`. It does **not** render to SVG. Rendering
hand-off is a separate step (the emitted folder includes a `render.sh` script
and a `README.md` that documents three rendering paths: GitHub native Mermaid
in `.md`, `npx mmdc` for SVGs, or Kroki HTTP for batch).

This separation preserves the v1 principle that the agent emits **text
artifacts** and never executes long-running toolchains.

## Source format conventions

Each emitted `<NN>-<slug>.md` follows the structural template (matches the
charge-relay convention, validated against 18 produced diagrams):

```
# D<N> — <Title>

**Audience:** Stakeholder | Engineer | Mixed
**When to look at this:** <one-line context for when this diagram helps>
**Prose gap closed:** <which existing prose artifact is too dense for this view>

---

## <Headline>

```mermaid
<diagram source>
```

---

## How to read this
<2-4 paragraphs of orientation>

### Key narrative
<the one sentence that summarises what the diagram is *saying*>

### What this diagram deliberately does *not* show
- <Pointer to sibling diagram(s) that cover the omitted angle>

---

## Legend
<color/shape/edge meanings>

---

## Back-links
- **Prose sources:** <paths>
- **Glossary:** `../4-glossary.md`
- **Strategic rationale:** <ADR or Cynefin frame reference>

---

*<NN> · Generated by /ddd:diagrams · Cited from kb/patterns/ddd-diagram-catalog.md#T<n.n> · Last updated: <date>*
```

The footer's `Cited from kb/patterns/ddd-diagram-catalog.md#T<n.n>` is the
**provenance anchor** required by CLAUDE.md hard-rule #4 (cite or don't
claim). Every emitted diagram MUST carry this footer.

## Numbering convention

Diagrams are numbered `01`, `02`, … in the order they're produced within a
session. The folder uses `0-diagrams/` prefix so it sorts before
`1-cynefin-framing.md` alphabetically — visuals first when a reader opens
`projects/<slug>/`.

The user's existing convention (charge-relay) suggests grouping diagrams by
theme in the per-folder `README.md` rather than the filename. This skill
adopts that convention.

## Mermaid as the rendering target

Mermaid is the only rendering target supported. Rationale:
- Renders natively in GitHub-flavoured Markdown — zero deps for browsers.
- `npx mmdc` produces SVGs locally with one command.
- Kroki.io renders Mermaid via HTTP for CI batch use.

Mermaid limitations the skill must honour:
- **No true pictographs.** Hofer/Schwentner Domain Storytelling pictographs
  need Excalidraw or illustrated PNGs. T4.3 emits a Mermaid flowchart with
  emoji glyphs as an approximation, with a header note explaining this.
- **No swimlane diagrams.** Mermaid sequence diagrams are vertical; a true
  Brandolini-style horizontal swimlane needs a different tool. The skill
  emits a labelled sequence diagram with participant grouping that
  approximates the swimlane.
- **Layout is best-effort.** Mermaid's auto-layout sometimes produces ugly
  edge crossings. Acceptable for `.md` source; a human can tweak before
  rendering.

## Cascade with `/ddd:revise`

When `/ddd:revise` classifies a change as `cascading`, the artifact-impact
matrix walk includes diagrams as a downstream artifact type (see
`kb/patterns/artifact-impact-matrix.md` § Diagrams as a downstream type).
The reviser:

1. Identifies which diagram IDs cite affected artifacts (greps the footer
   `Cited from kb/patterns/ddd-diagram-catalog.md#T<n.n>` and the
   `Back-links → Prose sources` section of each `0-diagrams/<NN>-*.md`).
2. Lists those diagram IDs in the manifest row's `diagrams-affected:` field.
3. Suggests `/ddd:diagrams --resync` as a follow-up step in Phase 5.

The user retains control: the reviser does **not** auto-regenerate
diagrams. The Plöd-L3 ceiling applies here too — the agent surfaces the
drift, the user decides whether to refresh.

## What this catalog protects against

1. **Diagram explosion.** Without tier discipline, projects accumulate
   diagrams that nobody reads. The trigger conditions in Tiers 2–4 force
   each diagram to earn its place.
2. **Domain-specific reinvention as "best practice."** Just because
   charge-relay produced a PAR-stack diagram doesn't mean every project
   should. The "out of scope" section names this trap explicitly.
3. **Drift between prose and visuals.** The cascade hook into `/ddd:revise`
   makes diagram drift detectable. Without it, diagrams silently lie about
   the model.
4. **Citation amnesia.** Every emitted diagram carries a kb-anchor in its
   footer so a future reader can verify the design rationale.

## Related references

- `kb/patterns/ddd-starter-modelling-process.md` — where in the 8-step process
  diagrams fit (after Step 5 Connect, alongside Step 6 Organise).
- `kb/patterns/context-mapping-patterns.md` — edge semantics consumed by T1.2.
- `kb/patterns/core-domain-chart.md` — feeds T2.1.
- `kb/patterns/aggregate-design-canvas.md` — complements T1.3 with tactical
  detail (kept in canvas form, not visualised in v1).
- `kb/voices/khononov-pragmatic-strategic.md` — coupling lens for T4.1.
- `kb/ceremonies/event-storming.md` — Big-Picture event ribbon basis (T1.4).
- `kb/ceremonies/domain-storytelling.md` — pictograph basis (T4.3).
- `kb/patterns/artifact-impact-matrix.md` § Diagrams — cascade hook spec.

## Attribution

- Tier-1 selection synthesised from Tune (*Domain-Driven Architecture
  Diagrams*, Medium 2021), Vernon (*Implementing DDD*), Evans (*DDD*),
  Brandolini (*EventStorming*).
- Tier-2/3/4 trigger conditions derived empirically from
  `projects/charge-relay/0-diagrams/` (18 diagrams, post-Phase-3-sync state)
  and validated against ddd-crew + Khononov.
- C4 alignment from Brown, *C4 Model* (https://c4model.com).
- Domain Storytelling notation from Hofer & Schwentner, *Domain Storytelling*.
- The "produces .md not .svg" handoff convention is native to this repo
  (charge-relay `render.sh` precedent).
