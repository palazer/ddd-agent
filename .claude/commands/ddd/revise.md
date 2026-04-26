---
description: Revise an existing DDD artifact and propagate consistent changes across the project graph. Optionally opens a co-revision dialog (Phase 0 Shape) before classification. Classifies the change kind, walks the artifact-impact matrix, and walks the user through per-artifact patches with AskUserQuestion gates.
argument-hint: "<artifact-path-or-change-description> --slug <project-slug> [--shape]"
---

# /ddd:revise

The **reactive peer** of `/ddd:ideate`. Use when you've already done
ideation and now want to change an artifact (or describe a change in
prose) and propagate the implications across the project graph
consistently. The optional **Phase 0 Shape** dialog lets you co-shape
the revision with the agent — read the artifact together, surface
refinement angles, and apply edits in place — before the sweep.

## When to use — and when not to

| Situation                                                                                          | Use                       |
|----------------------------------------------------------------------------------------------------|---------------------------|
| You are starting a new project from a blank sheet.                                                 | `/ddd:ideate`             |
| You want to modify **one** BC canvas and nothing else (no downstream propagation).                 | `/ddd:bc-canvas`          |
| You want to add or rename glossary terms only, with no downstream sweep required.                  | `/ddd:glossary`           |
| You changed (or want to change) an artifact and **are unsure what else needs updating**.           | `/ddd:revise`             |
| You renamed a term and want to sweep the project.                                                  | `/ddd:revise`             |
| You added a new Business Decision to a canvas and want to check it against ADRs + peer canvases.   | `/ddd:revise`             |
| You want to split a BC, change a context-map edge, or redefine an event schema.                    | `/ddd:revise`             |
| You want to **co-revise an artifact with the agent** (ideation-style dialog) AND sweep downstream. | `/ddd:revise --shape`     |
| You pointed at an artifact but haven't decided what to change yet.                                 | `/ddd:revise` (auto-offer)|

## Arguments

Three input shapes are supported.

**Shape A — point at an artifact you already modified:**
```
/ddd:revise projects/charge-relay/6-contexts/offer-authoring.md --slug charge-relay
```
The agent reads the file, derives a change summary (asking if ambiguous),
and proceeds. If the file has no delta since the last saved revision,
the agent offers to enter Phase 0 Shape (see Shape C).

**Shape B — describe the change in prose:**
```
/ddd:revise "rename Match-Decision-Log to Qualification-Decision-Log everywhere" --slug charge-relay
```
The agent identifies the target artifact type from the description and
proceeds. If the description is vague (no concrete names / "from X to Y" /
section reference), the agent offers to enter Phase 0 Shape (see Shape C).

**Shape C — co-revise with the agent (Phase 0 Shape, opt-in):**
```
/ddd:revise projects/charge-relay/6-contexts/offer-authoring.md --slug charge-relay --shape
```
The agent enters Phase 0 unconditionally. It surveys the target for
refinement angles (orphan terms, pile-up of open questions, Business
Decisions without ADRs, vague Purpose, etc.), lets you pick an angle or
propose one, drafts Before/After edits, and applies them live with a
per-edit `Apply / Refine / Hold` gate. When you pick `Proceed to
Triage`, the agent synthesises a `change_summary` and flows into the
normal Capture → Triage → Impact → Plan → Walk pipeline. Target edits
applied in Phase 0 are skipped by Phase 4 drafting — no double-patching.

`--slug` is required in all shapes. `/ddd:revise` is single-project —
it never crosses projects (CLAUDE.md §2).

## The 6-phase flow

```
┌──────────────────────────────────────────────────────────────────────┐
│ 0. Shape     (optional) co-revise target; survey angles; draft +     │
│              apply Before/After edits live                           │
│              [AskUserQuestion per survey + per draft + on exit]      │
│ 1. Capture   the triggering edit (Phase 0 output if it ran, else     │
│              the invocation delta)                                   │
│ 2. Triage    classify change kind → cosmetic | structural |          │
│              cascading                        [AskUserQuestion]      │
│ 3. Impact    walk the artifact-impact matrix + grep                  │
│                                               [AskUserQuestion]      │
│ 4. Plan      propose minimal consistent patches per artifact         │
│              (skips target-artifact patches already applied in       │
│              Phase 0; may route to ddd-interrogator for semantic     │
│              pushback)                                               │
│ 5. Walk      [AskUserQuestion per patch]; apply; sweep; audit        │
└──────────────────────────────────────────────────────────────────────┘
```

Three AskUserQuestion gates minimum (Phases 2, 3, and 5). Phase 0
adds several more (one survey gate plus one draft gate per round,
plus a change_summary confirmation on exit). Phase 5 gates once per
patch.

## Output

- `projects/<slug>/8-revisions/NNNN-<title>.md` — full revision record.
  Contains (when Phase 0 ran) a `## Phase 0 Shape Summary` section
  listing angles surveyed / chosen / discarded and edits applied; then
  `## Trigger`, `## Triage`, `## Impact analysis`, `## Proposed patches`
  (with `## Applied patches` after Phase 5), `## Deferred patches`,
  `## Sweep verification`, `## Operator verification`.
- Live edits to the target artifact applied during Phase 0 (if it ran).
- Per-patch edits applied to the impacted artifacts (canvases, glossary,
  context-map, ADRs) during Phase 5.
- One new row in `projects/<slug>/4-glossary.md § Drift Sweep Log` for
  every `cascading` revision.
- One new row in `projects/<slug>/4-glossary.md § Renaming Manifest` for
  every rename or redefinition.

## Behaviour

1. **Launch the `ddd-reviser` agent** with the arguments as context. The
   reviser drives the 6-phase loop.

2. **Phase 0 entry.** If `--shape` is passed, the reviser enters Phase 0
   unconditionally. Without the flag, the reviser detects no-delta or
   vague-prose invocations and presents a 3-option gate:
   `Yes, shape with me` / `No, treat as prose input` / `Abort`. Legacy
   Shape A (with a real file delta) and Shape B (with crisp prose) skip
   Phase 0 and go straight to Capture.

3. **Phase 0 loop.** In Phase 0, `revision-shape` skill runs in three
   modes: `survey` (returns refinement angles), `draft` (returns
   Before/After edit proposals), `apply` (writes the approved edit to
   the target via `Edit`). The reviser gates each transition. Phase 0
   never sweeps downstream, never classifies kind, never writes
   Manifest rows — that all remains Phases 2–5.

4. The **Triage gate** (Phase 2) is the first kind-classification
   checkpoint. If the change is `cosmetic`, Phases 3–5 short-circuit to
   a minimal record (no impact analysis, no sweep).

5. If `cascading`, expect a longer session: impact analysis across all
   downstream artifact types; per-patch walk with `ddd-interrogator`
   pushback for any patch that touches a BC boundary, an ADR decision,
   or a Business Decision.

6. The flow may pause at any AskUserQuestion — pick `Pause and save` and
   resume later. The draft `8-revisions/NNNN-*.draft.md` file is the
   resume anchor. Phase 0 flushes the draft file after every applied
   edit, so Phase 0 sessions are crash-safe.

## Preconditions

- `projects/<slug>/` exists and is a completed (or at least Step-6) DDD
  project. `/ddd:revise` does not bootstrap projects — use `/ddd:ideate`
  for that.
- `projects/<slug>/4-glossary.md` exists with a Renaming Manifest and
  Scope Fences.
- `kb/patterns/artifact-impact-matrix.md` is readable.

If any precondition fails, the reviser aborts with a clear explanation.

## Scope fences (honoured in every sweep)

- `projects/<slug>/input/**` — never read-beyond-summary, never written.
- Files declared `frozen` in the Renaming Manifest — never patched.
- Files declared `deferred` — flagged as deferred patches in the record,
  not applied.
- **Phase 0 Shape** additionally: refuses to edit any file other than
  the `target` path (sibling canvases, glossary, context-map are
  target-only in Phase 0; their propagation is Phases 2–5' job).

## Interrogator behaviours (enforced throughout)

- Pushback on anti-patterns is **not optional** (CLAUDE.md §5).
- Tactical code generation is **refused** (CLAUDE.md §3).
- Every rule applied must cite a `kb/` path (CLAUDE.md §4).
- All boundary decisions and semantic contradictions route to the user
  via `ddd-interrogator` (CLAUDE.md §1 / Plöd L3).
- Manifest row rewrites always trigger interrogator review.

## Related commands

- `/ddd:ideate` — the forward pipeline this command complements.
- `/ddd:bc-canvas` — fill/refine a single canvas (no downstream propagation).
- `/ddd:glossary` — lexical-only glossary edits (no matrix walk).
- `/ddd:example-map` — rule clarification on a single story.

## References

- `kb/patterns/artifact-impact-matrix.md` — matrix + change-kind taxonomy.
- `CLAUDE.md §6` — revision enforcement protocol (generalised Renaming Manifest).
- `.claude/agents/ddd-reviser.md` — orchestrator spec.
