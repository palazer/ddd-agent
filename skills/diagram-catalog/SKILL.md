---
name: diagram-catalog
description: Operational driver for /ddd:diagrams. Inspects project artifacts, recommends a tiered diagram set with reasoning, derives Mermaid sources from prose, and emits .md files to projects/<slug>/0-diagrams/. Interrogator-style — surfaces choices, never decides which diagrams to skip silently.
argument-hint: --slug <project-slug> [--resync] [--tier 1|1+2|all|custom]
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Skill, Agent, AskUserQuestion, SendMessage
---

# Diagram Catalog

The skill that powers `/ddd:diagrams`. Drives the 5-phase flow specified in
`commands/ddd/diagrams.md`.

**Canonical reference:** `kb/patterns/ddd-diagram-catalog.md` (tier
definitions, trigger conditions, structural template, Mermaid limitations).

## Preconditions

- `projects/<slug>/` exists.
- `kb/patterns/ddd-diagram-catalog.md` exists.
- At minimum `5-bc-candidates.md` and `6-contexts/` exist for T1.1 / T1.2
  to be eligible. Other diagrams have their own input requirements
  documented in the catalog.

If a precondition fails, abort with a clear message naming the missing
artifact and the diagram(s) that depend on it.

## Phase 1 — Discovery (silent compute)

Scan `projects/<slug>/` and produce a signals YAML. **No file writes; no
user gates.**

```yaml
signals:
  bcs:
    count: <N>
    names: [<bc-1>, <bc-2>, ...]
    classified_core_supporting_generic: <bool>      # T2.1 trigger
  actors:
    count: <N>                                       # T2.2 trigger (≥ 5)
    names: [<actor-1>, ...]
  external_systems:
    count: <N>                                       # T2.3 trigger (≥ 3)
    names: [<sys-1>, ...]
  pivotal_events:
    count: <N>                                       # T1.4, T3.1 trigger
    names: [<event-1>, ...]
  glossary_state_terms:                              # T1.5 trigger
    count: <N>
    terms: [<term-1>, ...]
  adrs:
    count: <N>                                       # T4.2 trigger (≥ 10)
    fallback_present: <bool>                         # T3.2 trigger
  party_roles:
    count: <N>                                       # T3.3 trigger (≥ 3)
  domain_stories_present: <bool>                     # T4.3 trigger
  khononov_cited: <bool>                             # T4.1 trigger
  existing_diagrams:                                 # for --resync mode
    - id: T1.2
      file: 02-bounded-context-map.md
      last_updated: 2026-04-19
      prose_sources: [../7-context-map.md, ../5-bc-candidates.md]
```

### How to detect each signal

| Signal | Detection rule |
|---|---|
| `bcs.count` | Count `### ` headings under `## Candidates` in `5-bc-candidates.md`; cross-check against `6-contexts/*.md` filenames. |
| `bcs.classified_core_supporting_generic` | Look for `Importance hint: Core` / `Supporting` / `Generic` lines in `5-bc-candidates.md`. |
| `actors.count` | Grep `2-event-storm.md` for actors section; count distinct names in `### Actors` block (or "yellow stickies"). |
| `external_systems.count` | Same source; `### External systems` block (or "pink stickies"). |
| `pivotal_events.count` | Look for `## Pivotal Events` or `### Pivotal events` heading in `2-event-storm.md`; count list items. |
| `glossary_state_terms` | Grep `4-glossary.md` for entries with a `**States:**` field or arrow notation `<S1> → <S2>`. |
| `adrs.count` | `Glob projects/<slug>/7-adr/*.md`. |
| `adrs.fallback_present` | Grep ADR contents for `fallback`, `degraded`, `timeout`, `graceful`, `circuit breaker`. |
| `party_roles` | Grep `4-glossary.md` for entries tagged with `Role:` or with `(role)` in the entry header. |
| `domain_stories_present` | `Read projects/<slug>/3-domain-stories.md` exists. |
| `khononov_cited` | Grep `projects/<slug>/**/*.md` for `khononov-` (kb path reference). |
| `existing_diagrams` | `Glob projects/<slug>/0-diagrams/*.md`; for each file, parse the footer `Cited from kb/patterns/ddd-diagram-catalog.md#T<n.n>` and the `Back-links → Prose sources:` line. |

### Compute the recommended set

For each tier ID:
- T1.x — eligible iff input artifacts exist (per catalog). Otherwise mark
  as `skipped` with reason.
- T2.1 — eligible iff `bcs.classified_core_supporting_generic == true`.
- T2.2 — eligible iff `actors.count >= 5` and `bcs.count >= 2`.
- T2.3 — eligible iff `external_systems.count >= 3`.
- T3.1 — eligible iff `pivotal_events.count >= 1`.
- T3.2 — eligible iff `adrs.fallback_present == true`.
- T3.3 — eligible iff `party_roles.count >= 3` and `bcs.count >= 2`.
- T4.1 — eligible iff `bcs.count >= 4` AND `khononov_cited == true`.
- T4.2 — eligible iff `adrs.count >= 10`.
- T4.3 — eligible iff `domain_stories_present == true`.

Output the full eligibility table in the Phase 2 recommendation —
including SKIPPED IDs with reasons. The user must see what was skipped
and why; this is non-negotiable per the Plöd-L3 ceiling (CLAUDE.md §1).

## Phase 2 — Recommendation (one AskUserQuestion gate)

Present the signals + eligibility table to the user. Use this canonical
shape:

- `question`: `"Recommended diagram set for <slug>: <N> Tier-1 + <M> qualifying Tier-2/3/4. Skipped IDs explained below. Proceed?"`
- `header`: `"Diagrams"`
- `options` (always exactly these four, in this order):
  1. `"Confirm Tier 1 only"` — proceed with the recommended Tier-1 set.
  2. `"Add Tier 2 expansion"` — Tier 1 + qualifying Tier 2.
  3. `"Add full set (T1+T2+T3+T4)"` — all qualifying IDs.
  4. `"Pick individually"` — re-prompt with multiSelect listing each
     qualifying ID separately.
- `multiSelect`: `false`.

**The question body must include the full eligibility table** with
skip reasons. Example:

```
Detected:
  - 5 BCs, 12 actors, 8 external systems, 2 pivotal events
  - 14 glossary terms (3 with state lifecycles)
  - 7 ADRs (1 with graceful-degradation reference)
  - 0 existing diagrams

Tier 1 (recommended):
  ✓ T1.1 Domain Capability Map
  ✓ T1.2 Bounded Context Map
  ✓ T1.3 Aggregate Conceptual Model
  ✓ T1.4 Event Ribbon
  ✓ T1.5 State Machines (Order, Payment, Shipment)

Tier 2 candidates (eligible):
  ✓ T2.2 Actor-Context Matrix (12 actors ≥ 5)
  ✓ T2.3 External Systems Landscape (8 ≥ 3)
  ✗ T2.1 Strategic Heatmap — skipped (no Core/Supporting/Generic
                              classification in 5-bc-candidates.md)

Tier 3 candidates (eligible):
  ✓ T3.1 Golden-path Sequence (2 pivotal events)
  ✓ T3.2 Fallback Decision Tree (ADR-0008 referenced)
  ✗ T3.3 Visibility Matrix — skipped (only 2 party roles ≥ 3 needed)

Tier 4 candidates (eligible):
  ✗ T4.1 Coupling Matrix — skipped (no khononov citations in prose)
  ✗ T4.2 ADR Dependency Graph — skipped (7 ADRs ≥ 10 needed)
  ✗ T4.3 Domain Stories Pictograph — skipped (no 3-domain-stories.md)
```

If the user picks `"Pick individually"`, re-issue `AskUserQuestion` with
`multiSelect: true` and one option per qualifying ID. Skipped IDs are
*not* in this multi-select — re-introducing them requires the user to
fix the underlying signal (e.g. add a Core Domain Chart classification).

## Phase 3 — Synthesise (one diagram at a time)

For each chosen diagram ID `<TIER-ID>`:

1. **Locate the template.**
   - Try `skills/diagram-catalog/templates/<TIER-ID>.md.tmpl`.
   - Templates ship for all 14 catalog IDs (`T1.1.md.tmpl` through
     `T4.3.md.tmpl`). The Mermaid type for each is locked in
     `kb/patterns/ddd-diagram-catalog.md § Mermaid type per diagram` —
     do not substitute a different Mermaid type without amending both
     the catalog table and the template.
   - If a future catalog row is added without a corresponding template,
     fall back to the generic structural template embedded in this
     skill (see § Generic structural template below).
   - The hybrid templates (T2.2, T3.3, T4.1) emit a markdown table
     **and** a Mermaid block. The table is canonical; the companion
     flowchart shows only the high-touch / high-privilege / high-coupling
     subset to keep the visual readable.

2. **Read the prose sources** specified in the catalog row for the ID.

3. **Derive the Mermaid block.** This is the load-bearing step. Rules:
   - Every label is a glossary term — verbatim, casing-preserved.
   - If a needed label is missing from `4-glossary.md`, **route to
     `glossary-curator`** (Skill / Agent) to surface it as a candidate.
     Do NOT invent the label.
   - Use the project's existing color legend if `0-diagrams/README.md`
     already exists (the user may have customised the palette). Otherwise
     use the catalog default (🟥 Core, 🟧 Supporting, 🟦 H2/Optional, ⬜
     Deferred/External).
   - Edge labels follow `kb/patterns/context-mapping-patterns.md` for
     T1.2 (context map): SK / OHS / PL / ACL / CF / SW.
   - Mermaid limitations honoured (per catalog § Mermaid as the
     rendering target).

4. **Substitute into the template.** Each template has placeholders the
   skill replaces:
   - `<HEADLINE>` — one-line "what this answers"
   - `<MERMAID_BLOCK>` — the derived diagram source
   - `<HOW_TO_READ>` — 2-4 paragraph orientation
   - `<KEY_NARRATIVE>` — one sentence
   - `<DELIBERATELY_NOT_SHOWN>` — list of pointers to sibling diagrams
   - `<LEGEND>` — color/shape/edge meanings
   - `<PROSE_SOURCES>` — bracketed paths
   - `<TIER_CITATION>` — `T<n.n>` (must match catalog ID)

5. **Write** to `projects/<slug>/0-diagrams/<NN>-<diagram-slug>.md`.
   `<NN>` is the next sequential 2-digit number based on existing
   diagrams. `<diagram-slug>` is fixed per ID:
   - T1.1 → `domain-capability-map`
   - T1.2 → `bounded-context-map`
   - T1.3 → `aggregate-conceptual-model`
   - T1.4 → `event-ribbon`
   - T1.5 → `state-machines`
   - T2.1 → `strategic-classification-heatmap`
   - T2.2 → `actor-context-matrix`
   - T2.3 → `external-systems-landscape`
   - T3.1 → `golden-path-sequence`
   - T3.2 → `fallback-decision-tree`
   - T3.3 → `visibility-matrix`
   - T4.1 → `coupling-volatility-matrix`
   - T4.2 → `adr-dependency-graph`
   - T4.3 → `domain-stories-pictograph`

6. **Footer line is mandatory.** Every emitted file ends with:
   ```
   *<NN> · Generated by /ddd:diagrams · Cited from kb/patterns/ddd-diagram-catalog.md#T<n.n> · Last updated: <YYYY-MM-DD>*
   ```
   This line is parsed by `/ddd:revise` to detect drift. Do not change
   the format without updating `kb/patterns/artifact-impact-matrix.md §
   Diagrams as a downstream type`.

### Generic structural template (used when a per-tier template doesn't ship)

```markdown
# D<NN> — <Title>

**Audience:** <Stakeholder | Engineer | Mixed>
**When to look at this:** <one-line context>
**Prose gap closed:** <which existing prose artifact this complements>

---

## <HEADLINE>

```mermaid
<MERMAID_BLOCK>
```

---

## How to read this

<HOW_TO_READ>

### Key narrative

<KEY_NARRATIVE>

### What this diagram deliberately does *not* show

<DELIBERATELY_NOT_SHOWN>

---

## Legend

<LEGEND>

---

## Back-links

- **Prose sources:** <PROSE_SOURCES>
- **Glossary:** [`../4-glossary.md`](../4-glossary.md)
- **Strategic rationale:** <STRATEGIC_RATIONALE>

---

*<NN> · Generated by /ddd:diagrams · Cited from kb/patterns/ddd-diagram-catalog.md#<TIER_CITATION> · Last updated: <DATE>*
```

## Phase 4 — Hand-off

After all chosen diagrams are emitted:

1. **Render script.** Read
   `skills/diagram-catalog/templates/render.sh.tmpl` and write to
   `projects/<slug>/0-diagrams/render.sh`. Make executable: `chmod +x`.
   - If `render.sh` already exists, do NOT overwrite. The user may have
     customised. Skip with a note.

2. **Folder README.** Read
   `skills/diagram-catalog/templates/diagrams-readme.md.tmpl` and
   substitute the per-diagram entries based on what was emitted in Phase
   3. Write to `projects/<slug>/0-diagrams/README.md`.
   - If `README.md` already exists, propose a **merge patch** rather
     than overwriting. The user's existing reading-order and audience-
     tilt grouping may differ from the template default.

## Phase 5 — Suggest follow-ups (one AskUserQuestion)

Final gate:

- `question`: `"Diagrams written to projects/<slug>/0-diagrams/. Render now?"`
- `header`: `"Render"`
- `options`:
  1. `"Run ./render.sh now"` — confirm; the agent invokes
     `bash projects/<slug>/0-diagrams/render.sh` via `Bash` tool. Returns
     the produced SVG list.
  2. `"Skip — view in GitHub Markdown"` — done.
  3. `"Pause for review"` — leave files as-is.

## --resync mode

When invoked with `--resync`:

1. **Phase 1** scans existing `0-diagrams/<NN>-*.md` files. Parses each
   for: footer `Last updated:` date, footer tier citation `T<n.n>`,
   `Back-links → Prose sources:` list.

2. **Phase 1.5** (resync-only): for each diagram, check whether any of
   its prose sources have a modification time later than the diagram's
   `Last updated:` date. Sources of truth, in order:
   - `projects/<slug>/4-glossary.md § Drift Sweep Log` rows newer than
     the diagram date.
   - `projects/<slug>/8-revisions/NNNN-*.md` records newer than the
     diagram date.
   - File-system mtime as last fallback.

3. **Phase 2** present the affected list:
   ```
   Resync analysis for <slug>:
     T1.2 Context Map (last 2026-04-19) — AFFECTED. Source 7-context-map.md
            modified by revision 0007 (2026-04-23).
     T1.4 Event Ribbon (last 2026-04-20) — UNAFFECTED.
     T3.2 Fallback Tree (last 2026-04-15) — AFFECTED. Source 7-adr/0008-*.md
            modified by revision 0009 (2026-04-24).
   ```
   Gate: confirm regenerate-all-affected / pick individually / skip.

4. **Phases 3-4** run only over confirmed-affected IDs. Phase 5 still
   gates render.

5. **Important**: `--resync` never *adds* new diagram IDs. It only
   refreshes existing ones. To add a new ID after a project matures,
   re-run `/ddd:diagrams` without `--resync`.

## Glossary-curator routing

If during Phase 3 a diagram needs a label not present in
`4-glossary.md`, do NOT invent it. Two options:

- **Inline route** (preferred for low-volume cases): invoke the
  `glossary-curator` agent via `Skill` with the missing-term context.
  Wait for the agent to either confirm the term should be added or
  surface a synonym from elsewhere in the project. Resume diagram
  synthesis once resolved.
- **Defer route** (for high-volume cases — > 3 missing terms in one
  diagram): pause synthesis, surface the full missing-term list to the
  user via `AskUserQuestion`, suggest running `/ddd:glossary` first.

The default is the inline route. Defer only when the diagram needs
fundamental glossary work, not just one or two missing labels.

## What this skill never does

- Does not generate tactical code (CLAUDE.md §3).
- Does not invent labels not in the glossary (CLAUDE.md §2).
- Does not auto-render SVGs (Phase 5 hand-off only; render.sh is opt-in).
- Does not edit prose artifacts. Diagrams are downstream — back-edges
  surface candidates via `glossary-curator`, never patches.
- Does not generate the out-of-scope diagrams (multi-party flow,
  layered validation, predicate pipeline) per the catalog.
- Does not skip Phase 2 (recommendation gate). Even with `--tier`
  preset, the agent still confirms the full set before synthesis.

## What this skill always does

- Surfaces SKIPPED IDs with reasons in Phase 2 (Plöd-L3 ceiling).
- Cites `kb/patterns/ddd-diagram-catalog.md#T<n.n>` in every emitted
  footer (CLAUDE.md §4).
- Honours the user's existing color/legend conventions if a folder
  README already exists.
- Suggests `/ddd:diagrams --resync` after `/ddd:revise` runs that
  classify as `cascading`.

## References

- `kb/patterns/ddd-diagram-catalog.md` — tier definitions, structural
  template, Mermaid limitations.
- `kb/patterns/artifact-impact-matrix.md § Diagrams as a downstream type`
  — cascade detection mechanics.
- `kb/patterns/context-mapping-patterns.md` — edge label semantics for T1.2.
- `kb/patterns/bounded-context-archetypes.md` — role taxonomy reused in T1.1.
- `kb/ceremonies/event-storming.md` — pivotal-event basis for T1.4.
- `agents/glossary-curator.md` — missing-label routing target.
- `agents/ddd-reviser.md` — surfaces `diagrams-affected` field.
- `commands/ddd/diagrams.md` — command shell.

## Attribution

Native to this repo. Tier curation derived from synthesis in
`kb/patterns/ddd-diagram-catalog.md` (which cites Tune, Brandolini,
Khononov, Vernon, ddd-crew, Brown, Hofer/Schwentner). Operational
patterns (5-phase flow, AskUserQuestion gates, glossary routing,
resync mode) follow conventions established in `event-storming` and
`revision-triage` skills.
