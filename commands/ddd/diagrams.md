---
description: Generate Mermaid diagram sources for a DDD project's artifacts. Interview-driven; recommends a Tier-1 default set with reasoning, offers Tier 2/3/4 expansions based on detected signals. Emits .md sources only; render hand-off via included render.sh.
argument-hint: "--slug <project-slug> [--resync] [--tier 1|1+2|all|custom]"
---

# /ddd:diagrams

Produce Mermaid diagram sources that visually complement a project's prose
artifacts. The skill is **interrogator-style**: it inspects the project,
recommends a curated set with explicit reasoning, and gates each tier
expansion behind `AskUserQuestion` rather than dumping all 14 candidate
diagrams on the user.

Canonical curation: [`kb/patterns/ddd-diagram-catalog.md`](../../../kb/patterns/ddd-diagram-catalog.md).

## When to use — and when not to

| Situation                                                                         | Use                          |
|-----------------------------------------------------------------------------------|------------------------------|
| You finished `/ddd:ideate` and want a visual companion to the prose artifacts.    | `/ddd:diagrams`              |
| You just finished `/ddd:revise` for a cascading change and diagrams may be stale. | `/ddd:diagrams --resync`     |
| You only want one specific diagram type and know its ID.                          | `/ddd:diagrams --tier custom` |
| You want SVGs rendered automatically.                                             | This skill does NOT render. Run the emitted `0-diagrams/render.sh` afterwards. |
| You want to generate aggregates / entities / framework code.                      | Out of scope (CLAUDE.md §3). |

## Arguments

- `--slug <project-slug>` — **required**. Which project to diagram.
  Single-project skill — never crosses projects (CLAUDE.md §2).
- `--resync` — **optional**. Walk the artifact-impact matrix backwards to
  find diagrams whose prose sources changed since their last update;
  propose per-diagram regeneration. Mirrors `/ddd:revise` per-patch walk.
- `--tier 1|1+2|all|custom` — **optional**. Pre-select tier scope,
  bypassing the interactive recommendation gate.
  - `1` (default if not specified, but still confirms) — Tier 1 only.
  - `1+2` — Tier 1 and Tier 2.
  - `all` — Tier 1, 2, 3, 4 (Tier 3/4 still gated by trigger conditions).
  - `custom` — open interview from scratch with no presets.

## The 5-phase flow

```
┌──────────────────────────────────────────────────────────────────┐
│ 1. Discovery   silently scan projects/<slug>/. Count BCs, ADRs,  │
│                actors, pivotal events, state-bearing terms.      │
│                Detect existing 0-diagrams/. Compute recommended  │
│                set with explicit skip-rule reasoning.            │
│                                            [no user gate]       │
│ 2. Recommend   present the recommended set + skip rationale +    │
│                Tier 2/3/4 candidates with their triggers.        │
│                                            [AskUserQuestion]    │
│ 3. Synthesise  for each chosen diagram, derive the Mermaid       │
│                source from prose artifacts. Each file follows    │
│                the structural template from the catalog. Emits   │
│                projects/<slug>/0-diagrams/<NN>-<slug>.md.        │
│                                            [no user gate]       │
│ 4. Hand-off    emit projects/<slug>/0-diagrams/render.sh         │
│                (sanitised portable script) and                    │
│                projects/<slug>/0-diagrams/README.md (per-folder   │
│                index in the user's existing convention).         │
│                                            [no user gate]       │
│ 5. Suggest     offer two follow-ups:                              │
│                - run ./render.sh to produce SVGs                  │
│                - schedule a /ddd:diagrams --resync after the      │
│                  next /ddd:revise run                             │
│                                            [AskUserQuestion]    │
└──────────────────────────────────────────────────────────────────┘
```

## Phase 1 — Discovery (signals scanned)

Scanned per the `kb/patterns/ddd-diagram-catalog.md` trigger conditions:

| Signal | Source | Affects which tier IDs |
|---|---|---|
| BC count | `5-bc-candidates.md`, `6-contexts/` | T1.1, T1.2, T2.1, T4.1 |
| Actor count | distinct actors in `2-event-storm.md` | T2.2 |
| External-system count | pink stickies in `2-event-storm.md` | T2.3 |
| Pivotal-event count | `2-event-storm.md` § Pivotal Events | T1.4, T3.1 |
| State-bearing glossary terms | `4-glossary.md` (entries with `States:` field) | T1.5 |
| ADR count | files in `7-adr/` | T4.2 |
| Fallback / degradation ADR | grep ADRs for "fallback", "degraded", "timeout", "graceful" | T3.2 |
| Party-role count | distinct role nouns in `4-glossary.md` | T3.3 |
| Domain stories present? | `3-domain-stories.md` exists | T4.3 |
| Khononov coupling cited? | grep all artifacts for `khononov-` references | T4.1 |
| Existing diagrams | `0-diagrams/<NN>-*.md` files | resync candidates |

Discovery emits a structured signals YAML to the calling agent — no file
writes.

## Phase 2 — Recommendation (one AskUserQuestion)

The agent presents the recommended set with reasoning. Example output:

> **Detected:**
> - 5 BCs, 12 actors, 8 external systems, 2 pivotal events
> - 14 glossary terms (3 with States: lifecycles)
> - 7 ADRs (1 mentions "graceful degradation")
> - 0 existing diagrams
>
> **Recommended (Tier 1):**
> - T1.1 Domain Capability Map ✓
> - T1.2 Bounded Context Map ✓
> - T1.3 Aggregate Conceptual Model ✓
> - T1.4 Event Ribbon ✓
> - T1.5 State Machines (3 detected: `Order`, `Payment`, `Shipment`) ✓
>
> **Tier 2 candidates** (would add):
> - T2.2 Actor-Context Matrix (12 actors > threshold of 5)
> - T2.3 External Systems Landscape (8 ext systems > threshold of 3)
> - T2.1 Strategic Classification Heatmap — **skipping** (no Core Domain
>   Chart filled in `5-bc-candidates.md`)
>
> **Tier 3 candidates** (would add):
> - T3.1 Golden-path Sequence (2 pivotal events → 1-2 sequences)
> - T3.2 Fallback Decision Tree (ADR-0008 captures degradation)
> - T3.3 Visibility Matrix — **skipping** (only 2 distinct party roles)
>
> **Tier 4 candidates** (would add):
> - T4.1 Coupling Matrix — **skipping** (5 BCs but no Khononov references
>   in prose; tier definition requires both)
> - T4.2 ADR Dependency Graph — **skipping** (7 ADRs < threshold of 10)
>
> Proceed with Tier 1 (5 diagrams)?

Options (always exactly these four, in this order):
1. `Confirm Tier 1 only` — proceed with the 5 recommended.
2. `Add Tier 2 expansion` — proceed with Tier 1 + the qualifying Tier 2 IDs.
3. `Add full set (T1+T2+T3+T4)` — all qualifying IDs across all tiers.
4. `Pick individually` — re-prompt with multi-select listing each
   qualifying ID separately.

## Phase 3 — Synthesise (one diagram at a time)

For each chosen diagram ID:

1. Read the templates:
   - `kb/patterns/ddd-diagram-catalog.md` (canonical structural template)
   - `skills/diagram-catalog/templates/<TIER-ID>.md.tmpl` (Tier-1
     templates ship; Tier-2/3/4 fall back to the structural template with
     a placeholder Mermaid block).

2. Read the prose sources listed in the trigger row.

3. Derive the Mermaid block by **citing project glossary terms verbatim**
   (CLAUDE.md §2). Refuse to invent labels. If a label is missing from
   the glossary, route to `glossary-curator` to surface the candidate.

4. Emit `projects/<slug>/0-diagrams/<NN>-<slug>.md` with the canonical
   footer: `*<NN> · Generated by /ddd:diagrams · Cited from kb/patterns/ddd-diagram-catalog.md#T<n.n> · Last updated: <date>*`.

The skill never edits sibling diagrams or prose artifacts during synthesis.
Drift between an emitted diagram and a peer is surfaced at Phase 5, not
fixed here.

## Phase 4 — Hand-off

After all chosen diagrams are emitted:

- Emit `projects/<slug>/0-diagrams/render.sh` from the templated copy.
- Emit `projects/<slug>/0-diagrams/README.md` listing all emitted
  diagrams in the user's tier-grouped convention (Fast path / Strategic /
  Conceptual / Behavior / Integration / Reference) with per-diagram
  audience tilt and reading time.

Both files are templated. The user can edit them freely.

## Phase 5 — Suggest follow-ups

Final `AskUserQuestion`:

> Diagrams written to `projects/<slug>/0-diagrams/`. Suggested next steps:

Options:
1. `Run ./render.sh now (requires npx)` — confirm and the agent invokes
   `bash projects/<slug>/0-diagrams/render.sh` (one-off; documented in
   the README that this is opt-in, not part of the skill itself).
2. `Skip render — view in GitHub-flavoured Markdown` — done. The user
   can render later.
3. `Pause and review` — leave the .md files for the user to review.

## --resync mode behaviour

When invoked with `--resync`:

1. **Phase 1** scans `0-diagrams/<NN>-*.md` files and parses each
   diagram's `## Back-links → Prose sources:` list.
2. **Phase 1.5** reads the project's `4-glossary.md § Drift Sweep Log`
   and the most recent `8-revisions/NNNN-*.md`. Computes which prose
   files have changed since each diagram's `Last updated:` footer date.
3. **Phase 2** presents the affected list with reasoning:
   > "T1.2 Context Map (last updated 2026-04-19) cites
   > `7-context-map.md` which was rewritten by revision 0007. Suggest
   > regenerate. T1.4 Event Ribbon — no upstream changes since last
   > update, skip."
4. **Phases 3-5** run only over confirmed-affected diagrams.

`--resync` never produces new diagram IDs that weren't already in the
project's `0-diagrams/`. It only refreshes existing files.

## Skills/agents invoked

- `diagram-catalog` skill (`skills/diagram-catalog/SKILL.md`) —
  the operational driver of all 5 phases.
- `glossary-curator` agent — when a Mermaid block needs a label not in
  the glossary, route to glossary-curator rather than inventing it.

## Outputs

```
projects/<slug>/0-diagrams/
├── README.md                       (per-folder index, audience-tilted reading order)
├── render.sh                       (portable Mermaid render script)
├── 01-domain-capability-map.md     (T1.1)
├── 02-bounded-context-map.md       (T1.2)
├── 03-aggregate-conceptual-model.md (T1.3)
├── 04-event-ribbon.md              (T1.4)
├── 05-state-machines.md            (T1.5)
├── 06-actor-context-matrix.md      (T2.2, if Tier-2 chosen)
└── ...
```

Each `.md` carries a Mermaid block, narrative orientation, legend, and
back-links to prose sources. SVGs are produced **only** when the user
runs `./render.sh` (out of band).

## Preconditions

- `projects/<slug>/` exists.
- At minimum `5-bc-candidates.md` and `6-contexts/` exist (Step-5+ of
  `/ddd:ideate`). T1.1 and T1.2 require them.
- `kb/patterns/ddd-diagram-catalog.md` is readable.

If preconditions fail, the skill aborts with a clear message naming the
missing artifact (e.g. "T1.4 requires `2-event-storm.md` but none
found — run `/ddd:event-storm` first or skip T1.4").

## Interrogator behaviours (enforced throughout)

- **Tier-1 must always be offered.** No project skips T1.1 + T1.2 +
  T1.3 unless those upstream artifacts genuinely don't exist.
- **Out-of-scope diagrams refused.** Multi-party payment flows, layered
  validation stacks, predicate-resolution pipelines (per catalog) are
  not auto-generated; the skill emits a stub-with-pointer if user
  explicitly requests one of these.
- **Mermaid limitations are surfaced honestly.** T4.3 emits a
  Mermaid-pictograph approximation with a header note that true
  Hofer/Schwentner pictographs need Excalidraw.
- **Glossary terms used verbatim** (CLAUDE.md §2). Unknown labels route
  to `glossary-curator`.
- **Citations enforced** (CLAUDE.md §4). Every emitted file's footer
  carries a `kb/` anchor.

## Related commands

- `/ddd:ideate` — produces the prose artifacts this skill diagrams.
- `/ddd:revise --shape` — co-revise a prose artifact; afterwards run
  `/ddd:diagrams --resync` if the change was cascading.
- `/ddd:bc-canvas` — refine a single canvas; T1.1 and T2.1 may need
  resync.
- `/ddd:glossary` — refine glossary terms; T1.3, T1.5, T3.3 may need
  resync.

## References

- `kb/patterns/ddd-diagram-catalog.md` — 4-tier curation; trigger
  conditions; structural template; Mermaid limitations.
- `kb/patterns/artifact-impact-matrix.md § Diagrams as a downstream
  type` — cascade hook spec.
- `skills/diagram-catalog/SKILL.md` — operational driver.
- `agents/ddd-reviser.md` — surfaces `diagrams-affected:` field
  in manifest rows for cascading revisions.
