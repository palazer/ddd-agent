---
title: Artifact Impact Matrix
source: derived from DDD ceremony flow; charge-relay empirical evidence (2026-04)
author: native (ddd-agent project)
license: CC BY 4.0
tier: 1
tags: [revision, dependency-graph, sweep, ddd-revise]
ingested: 2026-04-21
vendored_from: native
adaptation_notes: First-class reference for `/ddd:revise`. Formalises the dependency graph between the 9 DDD-ideation artifact types plus the change-kind taxonomy used by `revision-triage`, `impact-analysis`, and `revision-plan` skills.
---

# Artifact Impact Matrix

The 9 artifact types in `projects/<slug>/` form a **DAG with back-edges**,
not a tree. When a user edits one artifact, the edit may semantically
perturb others: a glossary rename propagates into every BC canvas's
Ubiquitous Language section; a context-map edge change reopens ADRs; a new
Business Decision on a canvas may contradict one in a peer canvas.

This matrix is the **machine-readable dependency graph** used by
`/ddd:revise` (see `.claude/commands/ddd/revise.md`). The
`impact-analysis` skill reads it to pre-assign priority; the
`revision-triage` skill reads it for the change-kind taxonomy.

## When to consult this matrix

- From `revision-triage` skill — to classify a change kind and look up the
  set of downstream artifact types worth checking.
- From `impact-analysis` skill — after triage, to rank each impacted
  artifact by priority.
- From `ddd-reviser` agent — in Phase 4 (revision plan) when deciding
  whether a patch warrants invoking `ddd-interrogator` for semantic
  pushback (CRITICAL and HIGH priority hits usually do).
- From humans reviewing a `projects/<slug>/8-revisions/NNNN-*.md` record
  — to check whether the recorded impact scope is complete.

## Change kinds (taxonomy)

Every revision `/ddd:revise` processes is first classified into one of
three kinds. The kind determines blast radius.

### `cosmetic`
- Wording, typos, example values that do **not** change semantics.
- Examples: fixing a spelling in a canvas Purpose section; reordering
  bullets; clarifying an Assumption without changing its content.
- **Downstream:** none. The triage skill returns an empty impact list.
- **Manifest entry:** not required. A footer audit-trail line in the
  touched file is sufficient.

### `structural`
- Touches the artifact's own semantics but is **contract-local** — it does
  not change a shared name, event schema, BC boundary, or map edge.
- Examples: adding a new Assumption to a canvas § 9; refining a Business
  Decision's rationale without changing the rule itself; adding a Verification
  Metric; clarifying an ADR's Consequences section.
- **Downstream:** usually 0–2 close neighbours (e.g. an ADR that cites the
  same Assumption may want re-reading).
- **Manifest entry:** optional; record under a new "Structural Revisions"
  subsection of the manifest if the impact touches more than one file.

### `cascading`
- Touches **shared semantics** — a glossary term, an event/command name, a
  BC name or boundary, a context-map edge label or relationship type.
- Examples: renaming `Settlement` → `Reconciliation` (term); splitting
  `CPO Inventory` into `CPO Inventory` + `CPO Onboarding` (BC boundary);
  flipping an edge from Shared Kernel to Customer/Supplier (relationship);
  adding a new outbound event on a canvas (interface).
- **Downstream:** follow the matrix table below.
- **Manifest entry:** **mandatory**. Every cascading revision writes a row
  to `projects/<slug>/4-glossary.md`'s Renaming Manifest (extended beyond
  literal renames — the schema carries a `Kind` column). The drift sweep is
  mandatory and its result is recorded in the Drift Sweep Log.

## Matrix

Read as: *when the artifact in the left column is edited (with a change of
kind `structural` or `cascading`), the artifacts in the middle column are
candidate-impacted and should be inspected*. The mechanism column states
**why**; the priority column is the default priority assigned by
`impact-analysis` and may be narrowed by the user at the Phase 3 checkpoint.

| Change in                 | Potentially impacts                                                                | Mechanism                                                         | Default priority |
|---------------------------|-------------------------------------------------------------------------------------|-------------------------------------------------------------------|------------------|
| `1-cynefin-framing.md`    | `2-event-storm.md`, `plan.md`, `0-presentation.md` (T1 q1–2, T2 Cynefin section)   | Hotspots re-map; core-domain classification shifts; exec surface and strategic frame re-derive | HIGH             |
| `2-event-storm.md`        | `3-domain-stories.md`, `4-glossary.md`, `5-bc-candidates.md`, `7-adr/*`, `0-diagrams/*` (T1.4, T2.2, T2.3, T3.1), `0-presentation.md` (T1 q3 actors) | Events ground narratives; surface terms; pivotal events suggest BC boundaries; ADRs may cite schema; event ribbon + actor matrix + external landscape + golden-path sequences re-derive; actor list in exec surface re-derives | HIGH             |
| `3-domain-stories.md`     | `4-glossary.md`, `5-bc-candidates.md`, `0-diagrams/*` (T4.3)                       | Stories mine terms; workflows surface BCs; Domain-storytelling pictograph re-derives | MEDIUM           |
| `3b-example-maps/<s>.md`  | `6-contexts/<bc>.md` § Business Decisions                                          | Rules become canvas § 8 entries                                    | MEDIUM           |
| `4-glossary.md`           | `5-bc-candidates.md`, `6-contexts/*.md`, `7-context-map.md`, `7-adr/*`, `0-diagrams/*` (T1.3, T1.5, T3.3), `0-presentation.md` (all tiers, verbatim) | Renaming Manifest cascade; UL consistency enforced across all downstream artifacts; aggregate model + state machines + visibility matrix carry glossary terms verbatim; presentation cites every term verbatim across all four tiers | CRITICAL         |
| `5-bc-candidates.md`      | `6-contexts/*.md`, `7-context-map.md`, `7-adr/*`, `0-diagrams/*` (T1.1, T2.1), `0-presentation.md` (T1 q4, T2 classification, T3 candidate list) | BC boundaries determine canvas set + context-map edges + ADR references; capability map + strategic-classification heatmap re-derive; BC list and classification table in presentation re-derive | HIGH             |
| `6-contexts/<bc>.md`      | `7-context-map.md`, `7-adr/*`, `4-glossary.md`, `0-diagrams/*` (T1.1, T1.3, T2.2), `0-presentation.md` (T3 that BC's subsection, T2 risks/strengths) | Inbound/outbound ↔ map edges; UL drift in § 7 triggers glossary sweep; capability map + aggregate model + actor-context matrix carry canvas labels; per-BC subsection in presentation re-derives, risks/strengths re-mine canvas § 10–11 | HIGH             |
| `7-context-map.md`        | `7-adr/*`, `6-contexts/*.md`, `0-diagrams/*` (T1.2, T4.1), `0-presentation.md` (T1 q5, T2 context-map narrative) | Edge redefinition → ADR revisit; canvas communication roles shift; context-map diagram + coupling matrix re-derive; relationship narrative in exec surface and strategic frame re-derive | HIGH             |
| `7-adr/<adr>.md`          | `5-bc-candidates.md`, `7-context-map.md`, `4-glossary.md`, `0-diagrams/*` (T3.2, T4.2), `0-presentation.md` (T2 risks list, T4 ADR table) | Accepted/superseded decisions may change BC set, map, or terms; fallback decision tree + ADR dependency graph re-derive; T4 ADR table refreshes on every new ADR (LOW for T4, MEDIUM if the ADR also reshapes T2 risks) | MEDIUM           |
| `8-revisions/<rev>.md`    | `0-presentation.md` (T4 revision table)                                            | T4 appendix lists every revision by number/kind; new revisions extend the table | LOW              |
| `0-diagrams/<NN>-*.md`    | (rare) `4-glossary.md` if a diagram surfaces a missing term                        | Diagrams are leaves of the dependency graph; only back-edge is a glossary candidate surfaced during diagramming | LOW              |
| `0-presentation.md`       | (rare) `4-glossary.md` if synthesis surfaces a term used upstream but missing from the glossary | Presentation is derivative-only — the only back-edge is a glossary candidate surfaced when an upstream artifact references a term that was never glossary-promoted | LOW              |
| `plan.md` (if present)    | All of the above                                                                   | Pre-DDD constraints re-verified through the ceremony chain         | CRITICAL         |

### Notes on asymmetry

- **Back-edges exist.** `6-contexts/<bc>.md` points *up* to `4-glossary.md`
  because a canvas may surface a new term worth promoting to the glossary.
  Revising a canvas does **not** automatically rewrite the glossary — it
  surfaces a candidate the glossary-curator agent should consider.
- **ADRs are terminal-ish.** ADRs rarely drive changes in other artifacts
  directly; they *record* decisions made elsewhere. But a **new ADR** that
  changes a BC boundary or an edge is effectively a `cascading` change —
  the impact flows back through the matrix.
- **Event storm is load-bearing upstream.** Renaming an event surfaced in
  `2-event-storm.md` has the same blast radius as renaming a glossary
  term — both are shared names consumed by canvases and ADRs.
- **Diagrams are leaves.** `0-diagrams/<NN>-*.md` files are downstream of
  most prose artifacts and upstream of almost nothing. The single
  back-edge is when a diagramming session surfaces a glossary candidate —
  same mechanism as the canvas → glossary back-edge.
- **The presentation is a leaf too.** `0-presentation.md` is the prose
  peer of `0-diagrams/`: derivative-only, downstream of every other
  artifact, upstream of nothing except the same single glossary
  back-edge. A `cascading` change anywhere in `1-` through `8-` may
  propagate to one or more of its four tiers — see § Presentation as a
  downstream type below.

## Diagrams as a downstream type

`0-diagrams/<NN>-*.md` files (produced by `/ddd:diagrams` per
`kb/patterns/ddd-diagram-catalog.md`) are first-class downstream artifacts.
The matrix table above includes them inline; this section documents the
mechanics.

**How the cascade detects affected diagrams.** Each emitted diagram file
carries two provenance markers in its source:

1. A footer line: `*<NN> · Generated by /ddd:diagrams · Cited from kb/patterns/ddd-diagram-catalog.md#T<n.n> · Last updated: <date>*`.
2. A `## Back-links` section that lists every prose source the diagram cites
   (e.g. `**Prose sources:** [../6-contexts/<bc>.md], [../4-glossary.md]`).

When `/ddd:revise` walks the matrix for a `cascading` change, it greps the
back-links across all `0-diagrams/<NN>-*.md` files in the project. Any file
whose Prose Sources list includes the changed artifact is added to the
manifest row's `diagrams-affected:` field.

**The reviser does not auto-regenerate.** Plöd-L3 ceiling applies to
diagrams as it does to canvases: the reviser surfaces drift, the user runs
`/ddd:diagrams --resync` if they want to refresh.

**Tier-aware drift.** Not every prose change ripples to every tier:
- A glossary rename impacts T1.3, T1.5, T3.3 (diagrams using glossary
  terms verbatim) — HIGH priority.
- A new ADR impacts T4.2 (ADR dependency graph) only when N ≥ 10, else LOW.
- An event-name change impacts T1.4, T3.1 (event-bearing diagrams) — HIGH.
- A canvas Purpose-section rewrite impacts none of the diagrams (Purpose
  is prose; diagrams cite labels and roles, not narrative). Triage will
  classify this as `cosmetic` or `structural` and stop before the diagram
  walk.

**Resync vs regenerate.** `/ddd:diagrams --resync` walks the affected
list and proposes a per-diagram patch (Mermaid block re-derived from the
new prose); the user gates each patch separately, mirroring the
`/ddd:revise` per-patch walk pattern (CLAUDE.md §6).

## Presentation as a downstream type

`0-presentation.md` (produced by `/ddd:present` per
`kb/patterns/model-presentation-tiers.md`) is a first-class downstream
artifact, structurally parallel to `0-diagrams/`. The matrix table
above includes it inline; this section documents the mechanics.

**How the cascade detects affected tiers.** The emitted
`0-presentation.md` carries two provenance markers:

1. A footer line: `*0-presentation · Generated by /ddd:present · Cited from kb/patterns/model-presentation-tiers.md · Last updated: <date>*`.
2. A `## Back-links → Prose sources` section that lists every
   upstream artifact the presentation cites
   (`1-cynefin-framing.md`, `2-event-storm.md`, `4-glossary.md`,
   `5-bc-candidates.md`, `6-contexts/<bc-slug>.md` × N,
   `7-context-map.md`, `7-adr/*` × N, `8-revisions/*` × N,
   `0-diagrams/<NN>-*.md` × N).

When `/ddd:revise` walks the matrix for a `cascading` change, it greps
the back-links of `0-presentation.md`. If the presentation cites the
changed artifact, the manifest row's `presentation-affected:` field is
populated with the impacted tier IDs (T1, T2, T3, T4).

**The reviser does not auto-regenerate.** Plöd-L3 ceiling applies to
the presentation as it does to canvases and diagrams: the reviser
surfaces drift, the user runs `/ddd:present --resync` if they want to
refresh.

**Tier-aware drift.** Not every prose change ripples to every tier:

| Drift in upstream | Affects which presentation tier | Default priority |
|---|---|---|
| `4-glossary.md` term rename | All tiers (every tier cites glossary terms verbatim) | CRITICAL |
| `5-bc-candidates.md` (BC added/removed/reclassified) | T1 q4, T2 classification table, T3 candidate list | HIGH |
| `7-context-map.md` (edge added/removed/relabeled) | T1 q5, T2 context-map narrative | HIGH |
| `6-contexts/<bc>.md` (canvas semantics shift) | T3 (that BC's subsection only), T2 risks/strengths | HIGH |
| `1-cynefin-framing.md` rewrite | T1 q1–2, T2 Cynefin summary | HIGH |
| `2-event-storm.md` actor/event change | T1 q3 (actors list) | MEDIUM |
| New ADR in `7-adr/` | T2 risks list (if ADR carries a risk), T4 ADR table (always) | MEDIUM |
| New revision in `8-revisions/` | T4 revision table only | LOW |
| Canvas Purpose-section rewrite | T3 subsection of that BC (verbatim citation) | MEDIUM |
| Cosmetic edit (no semantic shift) | none — `/ddd:revise` triage stops earlier | — |

**Resync vs regenerate.** `/ddd:present --resync` walks the affected
tier list and proposes a per-tier patch (each tier's prose re-derived
from the new upstream); the user gates each tier patch separately,
mirroring the `/ddd:revise` and `/ddd:diagrams --resync` per-patch
walk patterns (CLAUDE.md §6).

**Tier scope is preserved across resync.** If the existing
`0-presentation.md` was emitted with `--tier 1+2`, a resync will not
introduce Tier 3 — that requires a fresh `/ddd:present` run with the
appropriate tier scope. The resync only refreshes tiers already
present in the file.

**Independence from `/ddd:diagrams`.** A `/ddd:present` run does not
invoke `/ddd:diagrams`, and a `/ddd:diagrams` run does not invoke
`/ddd:present`. They are independent peers — the presentation cites
whatever diagrams already exist in `0-diagrams/` and emits "(no
T<n.n> diagram present — run `/ddd:diagrams`)" stubs where a
diagram is expected but missing. A change to `0-diagrams/<NN>-*.md`
does **not** trigger a `presentation-affected:` row — diagrams are
referenced by ID, not by content quotation; their refresh is its own
concern.

## Scope fences

`/ddd:revise` honours the same scope fences as the Renaming Manifest
protocol in CLAUDE.md §6:

- `projects/<slug>/input/` — read-only to agents, **never swept, never patched**.
- Files declared `frozen` in the glossary's Renaming Manifest (external
  research, third-party API docs) — **skip entirely**. A reference to a
  renamed term inside a frozen file is not drift.
- Files declared `deferred` (legacy solution docs scheduled for later
  update) — flag with a defer note in the 8-revisions record; **do not
  patch**. The user may schedule a dedicated revision when appropriate.
- Files not in the `projects/<slug>/` tree — out of scope.

The skill that walks the matrix **must** read the project's
`4-glossary.md` Renaming Manifest scope-fence subsections before greping
— otherwise it will report false-positive drift in frozen files.

## Relationship to the Renaming Manifest

The Renaming Manifest in `4-glossary.md` was the **first operationalised
revision protocol** in this repo (CLAUDE.md §6). `/ddd:revise` generalises
it:

- Renaming Manifest was **term-only**. `/ddd:revise` records any
  `cascading` change kind in a manifest table of the same shape.
- Renaming Manifest had no triage step. `/ddd:revise` adds triage so that
  `cosmetic` and `structural` changes don't pay the sweep cost.
- Renaming Manifest embedded all change metadata in-line. `/ddd:revise`
  moves full records to `projects/<slug>/8-revisions/NNNN-*.md` and leaves
  a one-row pointer in the manifest.

The Drift Sweep Log table remains canonical — every sweep writes a row,
whether triggered by a bare `/ddd:glossary` rename or by a full
`/ddd:revise` run.

## Anti-patterns this matrix protects against

1. **Silent drift.** A glossary rename without a sweep leaves old terms in
   canvases and ADRs. The matrix makes that drift computable.
2. **Over-reporting.** Naïve greps across the whole repo return hits in
   frozen research docs. The scope fences prevent that.
3. **Under-reporting back-edges.** A canvas edit that surfaces a new
   term is easy to miss. The `6-contexts → 4-glossary` edge flags it.
4. **Treating ADRs as append-only in isolation.** An ADR that changes a BC
   boundary is a `cascading` change whose consequences must sweep back
   through the matrix. See ADR-0022 in charge-relay for a deferred-trigger
   example.

## Related references

- `CLAUDE.md §6` — Revision enforcement protocol (formerly Renaming
  enforcement).
- `kb/canonical/fowler-ubiquitous-language.md` — Ubiquitous Language
  consistency basis (the reason `4-glossary.md` is CRITICAL).
- `kb/patterns/context-mapping-patterns.md` — edge semantics consumed by
  `7-context-map.md`.
- `kb/patterns/bounded-context-archetypes.md` — role taxonomy used in
  canvas § 4 (Domain Roles).
- `kb/canonical/evans-bounded-context.md` — why BC boundary shifts are
  treated as `cascading` by default.

## Attribution

Derived from:
- Empirical evidence in `projects/charge-relay/` (8 BC canvases, 27 ADRs,
  4 completed Renaming Manifest sweeps) — see `projects/charge-relay/4-glossary.md`
  Drift Sweep Log for the evidence base that shaped the priority column.
- `CLAUDE.md §6` Renaming Manifest protocol (native to this repo).
- DDD ceremony artifact flow specified in
  `kb/patterns/ddd-starter-modelling-process.md`.
