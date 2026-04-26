---
title: Model Presentation Tiers (4-Tier Prose Companion)
source: derived from C4 zoom levels (Brown), Pyramid Principle (Minto), DDD ceremony artifact flow; charge-relay empirical evidence (2026-04)
author: native (ddd-agent project)
license: CC BY-SA 4.0
tier: 1
tags: [presentation, narrative, audience-graduation, ddd-present, third-party-handoff]
ingested: 2026-04-26
vendored_from: native
adaptation_notes: First-class reference for `/ddd:present`. Defines the 4-tier audience-graduated prose companion that lets a third party engage with a DDD project without walking the full ceremony evidence trail.
---

# Model Presentation Tiers

A 4-tier curation of how a DDD project's prose should be presented to readers
who did **not** walk the ceremonies themselves. Drives the
`/ddd:present` skill and produces the `0-presentation.md` artifact.

## Why a tier system at all

The artifacts produced by `/ddd:ideate` (`1-cynefin-framing.md` through
`7-context-map.md`) are **process records**: they preserve the order of
inquiry, the pushbacks, the abandoned hypotheses. That trail is load-bearing
for the user who walked it — and for the `/ddd:revise` reviser, which mines
it for context. It is **not** a product surface.

A third party reading the artifacts cold hits two problems:

1. **No depth grading.** Every artifact reads at one density. There is no
   "executive summary" surface, no mid-level "engineering tour" surface, no
   per-BC deep dive. The reader is forced to either skim everything
   superficially or commit to reading all 7+ files end to end.
2. **No audience cue.** The artifacts assume DDD literacy (Cynefin
   framings, BC archetypes, context-map relationship types). A non-DDD
   stakeholder cannot tell which paragraph is for them.

The literature on technical communication is consistent that **audience-
graduated depth** is the only reliable solution: present the answer first,
then the strategy, then the detail (Minto, *The Pyramid Principle*); zoom in
one level at a time (Brown, *C4 Model*); ship a TL;DR before the autopsy
(Klare, *Readability of Technical Documents*).

The four tiers below operationalise that into concrete sections, audience
labels, length budgets, and source-artifact citations.

## Hard rules

1. **The presentation is derivative-only.** Nothing in any tier may
   originate a fact. Every BC name, glossary term, decision, and
   relationship must already exist in an upstream artifact and must be
   cited verbatim. If the presentation surfaces a fact missing from
   upstream, that is a smell — the upstream artifact is the place to fix
   it, not the presentation.
2. **The presentation enters the impact matrix as a downstream consumer.**
   See `kb/patterns/artifact-impact-matrix.md § Presentation as a
   downstream type`. A `cascading` change in any upstream artifact
   triggers a `/ddd:present --resync` candidacy.
3. **Glossary terms used verbatim** (CLAUDE.md §2). The presentation
   never introduces a synonym.
4. **Citations enforced** (CLAUDE.md §4). Every claim cites either an
   upstream `projects/<slug>/` artifact or a `kb/` reference.
5. **The presentation does not replace the artifacts.** It is a cover
   sheet, not a rewrite. The Tier-4 appendix points the curious reader
   back to the source-of-truth artifacts and the revision log.

## The 4 tiers

### Tier 1 — Executive surface (TL;DR + The model in five questions)

**Audience:** anyone — exec, partner BU, prospective hire, stakeholder
without DDD literacy.

**Question answered:** "What is this thing, and why does it exist?"

**Length budget:** ½ page TL;DR + 1–2 pages on the five questions = ≤ 3 pages.

**Sections:**
- **TL;DR (5–8 sentences):** What domain, what the system does in business
  language, the 1–2 BCs that carry the differentiation, the most important
  open question. Ends with a one-sentence "read this next" pointer.
- **The model in five questions:**
  1. What domain are we in? (1 paragraph from `1-cynefin-framing.md`,
     business framing only)
  2. What is genuinely complex here? (1 paragraph — the Complex / Core
     classification from `1-cynefin-framing.md`)
  3. Who are the actors? (3–6 bullets from `2-event-storm.md` actors)
  4. What are the bounded contexts and how many are Core?
     (numbered list from `5-bc-candidates.md` with one-line purposes)
  5. How do the contexts relate? (1 paragraph + reference to T1.2
     diagram from `0-diagrams/`)

**Required upstream artifacts:** `1-cynefin-framing.md`,
`5-bc-candidates.md`. If `2-event-storm.md` exists the actors question
is filled; otherwise it is marked "TBD — pending event storm" rather than
fabricated.

**Pairs with diagrams:** T1.1 (Domain Capability Map), T1.2 (Context Map)
— see `kb/patterns/ddd-diagram-catalog.md § Tier 1`.

### Tier 2 — Strategic frame

**Audience:** engineering lead, architect, head of product, anyone
making investment decisions about which BCs to staff.

**Question answered:** "Where should attention and budget go, and why?"

**Length budget:** 3–5 pages.

**Sections:**
- **Cynefin framing summary** (1 page): the project's domains by
  Cynefin quadrant (Clear/Complicated/Complex/Chaotic), with the
  rationale for the Complex calls. From `1-cynefin-framing.md`.
- **Strategic classification table** (½ page): one row per BC with
  Core/Supporting/Generic, Revenue/Engagement/Compliance, Wardley
  evolution, Life expectancy. From `5-bc-candidates.md` and each
  `6-contexts/<bc>.md § 3`.
- **Context map narrative** (1 page): each non-trivial edge between
  BCs in plain language — what flows, what direction, what relationship
  pattern (Customer/Supplier, Anti-Corruption Layer, Shared Kernel,
  Open Host Service, Conformist, Partnership, Published Language).
  Cites `kb/patterns/context-mapping-patterns.md` for the relationship
  semantics. References T1.2 / T2.1 diagrams from `0-diagrams/` if
  present.
- **Top 3 risks and top 3 strengths** (½ page): synthesised from the
  Open Questions sections of canvases (§ 11) and the Verification
  Metrics sections (§ 10). Each risk cites the canvas or ADR where it
  was first surfaced.

**Required upstream artifacts:** `1-cynefin-framing.md`,
`5-bc-candidates.md`, `7-context-map.md`. Tier 2 is **skipped** if any
of these are missing — the section emits a placeholder with a pointer
to the missing ceremony.

**Pairs with diagrams:** T2.1 (Strategic Classification Heatmap), T1.2
(Context Map), and — if present — T2.2 / T2.3.

### Tier 3 — Per-context detail

**Audience:** developers about to work on a specific BC.

**Question answered:** "What does this bounded context actually do, and
what should I read to make a change here?"

**Length budget:** ½ – 1 page per BC. A 5-BC project produces 2½ – 5
pages at Tier 3.

**Sections (one subsection per BC):**
- **Purpose** (verbatim from canvas § 2)
- **Strategic classification** (from canvas § 3 — one line)
- **Domain roles / archetypes** (from canvas § 4, citing
  `kb/patterns/bounded-context-archetypes.md`)
- **Inbound and outbound communication** (rendered as prose: "this BC
  receives X from Y; it emits Z to W"). From canvas § 5–6.
- **Key invariants and business decisions** (from canvas § 8 — the
  rules, not the rationale)
- **Open questions** (from canvas § 11 — the real ones, no padding)
- **Pointers:** "For depth, read `6-contexts/<bc>.md`. For visual:
  T1.3 aggregate model in `0-diagrams/`. For the rule lineage:
  `3b-example-maps/<story>.md`."

**Required upstream artifacts:** `6-contexts/*.md` (≥ 1 canvas).
Per-BC subsections are skipped for BCs that do not yet have a canvas;
the presentation lists them as "candidate, no canvas yet — see
`5-bc-candidates.md`".

**Pairs with diagrams:** T1.3 (Aggregate Conceptual Model), T1.5 (State
Machines), and per-BC slices of T2.2 / T3.3 if present.

### Tier 4 — Appendix (links only)

**Audience:** auditors, future-you, the next person running
`/ddd:revise`, anyone validating a claim made in Tiers 1–3.

**Question answered:** "Where did this come from, and what was the
reasoning trail?"

**Length budget:** ≤ 1 page — this tier is link-only.

**Sections:**
- **Source artifacts** — bullet list of every `projects/<slug>/*.md`
  with a one-line description and direct path link.
- **Architecture decisions** — table of `7-adr/*` files: ADR number,
  title, status, one-line summary.
- **Revision history** — table of `8-revisions/*` files: revision
  number, kind (cosmetic/structural/cascading), one-line summary.
- **Diagrams** — if `0-diagrams/` is populated, list each diagram
  by ID, audience tilt, last-updated date. Otherwise "no diagrams
  yet — run `/ddd:diagrams`".
- **Glossary entry point** — single link to `4-glossary.md` with a note
  that all terms used above are defined there.

**Required upstream artifacts:** none — Tier 4 only describes what
exists. Its rows fan out only to the artifacts that are actually
present.

**Pairs with diagrams:** none directly; T4.1 / T4.2 / T4.3 (if present)
are listed in the diagrams sub-table.

## Audience-tier mapping summary

| Tier | Audience | Question | Length | Required upstream |
|------|---------|----------|--------|-------------------|
| 1 | Exec / non-DDD | What is this and why? | ≤ 3 pp | Cynefin framing, BC candidates |
| 2 | Engineering lead / architect | Where to invest? | 3–5 pp | Cynefin, BC candidates, context map |
| 3 | Developer on a BC | What does this BC do? | ½–1 pp / BC | At least one canvas in `6-contexts/` |
| 4 | Auditor / future-you | Where did this come from? | ≤ 1 pp | (none — describes what exists) |

## Skip rules (must be explicit in the recommendation)

- **Tier 1 must always be offered.** A presentation without an executive
  surface is not a presentation — it's a longer ceremony record. Even if
  upstream is sparse, the skill produces a Tier-1 placeholder that names
  what is missing rather than skipping the tier.
- **Tier 2 is skipped if `7-context-map.md` is missing.** A strategic
  frame with no context-map narrative degrades to a list of BCs without
  relationships — strictly worse than no Tier 2.
- **Tier 3 lists candidate BCs without canvases as TBD.** Do not
  fabricate a per-BC subsection for a BC that has no canvas — the
  presentation must not invent purpose statements or invariants.
- **Tier 4 is always emitted, even on a sparse project.** Its job is to
  describe what exists. On a project with only `1-cynefin-framing.md`
  and `5-bc-candidates.md`, Tier 4 is a 4-line bullet list — that's
  fine.

## Resync semantics

When `/ddd:present` runs in `--resync` mode, it walks the
`artifact-impact-matrix.md` matrix backwards using the
`0-presentation.md` file's `## Back-links → Prose sources:` block
(the same provenance pattern used by `0-diagrams/`). Per-tier drift is
classified as:

| Drift in | Affects which tier | Default priority |
|---|---|---|
| `4-glossary.md` term | All tiers (verbatim term usage) | CRITICAL |
| `5-bc-candidates.md` (BC added/removed) | T1, T2, T3 | HIGH |
| `7-context-map.md` (edge added/removed/relabeled) | T2 | HIGH |
| `6-contexts/<bc>.md` (canvas semantics) | T3 (that BC's subsection) | HIGH |
| `1-cynefin-framing.md` rewrite | T1 question 1–2, T2 Cynefin summary | HIGH |
| `2-event-storm.md` actor change | T1 question 3 | MEDIUM |
| New ADR in `7-adr/` | T2 risks list, T4 ADR table | MEDIUM |
| New revision in `8-revisions/` | T4 revision table | LOW (link-only update) |
| Cosmetic edit (no semantic shift) | none — `/ddd:revise` triage stops earlier | — |

`/ddd:present --resync` mirrors `/ddd:diagrams --resync` in shape:
walks affected tiers, proposes per-tier patches, gates each behind
`AskUserQuestion` (Plöd-L3 ceiling, CLAUDE.md §1).

## Anti-patterns this tier system protects against

1. **The 50-page brain dump.** Concatenating every artifact into one
   document and calling it a "presentation" defeats the purpose. Each
   tier has a length budget; busting it is a smell.
2. **The polished lie.** Rewriting an artifact for the presentation in
   "cleaner" language strips the contested-decision context. The
   presentation cites — never paraphrases away — the source.
3. **The orphan summary.** A README hand-written outside the ceremony
   chain becomes drift the moment a glossary term changes. The
   presentation must enter the impact matrix or it lies within two
   revisions.
4. **The audience-confused middle.** Sections that mix exec framing
   with developer detail in one paragraph satisfy neither audience.
   Each tier targets one reader.
5. **The fake completeness.** Filling Tier 3 subsections for BCs that
   have no canvas, by inventing purposes from BC names. The skip rules
   above forbid this.

## Relationship to `kb/patterns/ddd-diagram-catalog.md`

The diagram catalog is the visual peer of this prose catalog. Each
prose tier pairs with the diagram tier(s) at the same audience level:

| Prose tier | Pairs with diagram tier(s) | Why |
|---|---|---|
| T1 (exec) | T1.1, T1.2 | Capability map + context map are the two diagrams a non-DDD reader can absorb |
| T2 (architect) | T2.1, T2.2, T2.3 | Strategic classification, actor-context, external systems — investment-decision lenses |
| T3 (developer) | T1.3, T1.5, T3.1, T3.3 | Aggregate model + state machines + golden-path sequences + visibility — the shapes a developer needs |
| T4 (audit) | T4.1, T4.2, T4.3 | Coupling matrix, ADR graph, story pictographs — the meta-views |

A `/ddd:present` run **does not invoke** `/ddd:diagrams` — they are
independent. The presentation cites whichever diagrams already exist
in `0-diagrams/` and emits a "(no T1.2 diagram present — run
`/ddd:diagrams`)" pointer where a diagram is expected but missing.
This avoids implicit cross-skill coupling.

## Related references

- `kb/patterns/ddd-diagram-catalog.md` — visual peer; tier audience model.
- `kb/patterns/artifact-impact-matrix.md` — dependency graph; the
  presentation enters as a downstream type.
- `kb/canonical/fowler-ubiquitous-language.md` — why glossary verbatim
  is non-negotiable in every tier.
- `kb/voices/khononov-pragmatic-strategic.md` — Core/Supporting/Generic
  classification consumed at T2.
- `CLAUDE.md §1` — Plöd-L3 ceiling; user gates per-tier patches in
  `--resync` mode.
- `CLAUDE.md §6` — Revision enforcement; presentation entered into
  Renaming Manifest cascade like any other downstream artifact.

## Attribution

Synthesised from:
- Brown, *The C4 Model for Software Architecture* — zoom-level audience
  graduation.
- Minto, *The Pyramid Principle* — answer-first → strategy → detail
  ordering.
- Native to this repo — the artifact-impact-matrix shape and the
  Plöd-L3 user-gate discipline.
- Empirical evidence in `projects/charge-relay/` — surfaced the
  third-party-handoff problem this catalog solves.
