---
name: revision-triage
description: Phase 2 of /ddd:revise. Classify a proposed revision as cosmetic | structural | cascading; extract the affected-name set; cite the artifact-impact matrix. Pure compute — no writes. Accepts Phase 0 Shape output as an alternative input when co-shaping preceded triage.
argument-hint: <change_summary> <target_artifact_type> [--slug <project-slug>] [--target-edits-pre-applied <yaml>]
allowed-tools: Read, Grep, Glob
---

# Revision Triage

Phase 2 skill for `/ddd:revise`. Given a human-readable change summary and
the target artifact type, classify the change by **kind** and return a
structured YAML block that the `impact-analysis` skill (Phase 3) and the
`ddd-reviser` agent (Phase 2 checkpoint) consume.

**Canonical reference:** `kb/patterns/artifact-impact-matrix.md` (change-
kind taxonomy + scope fences).

## Preconditions

- `projects/<slug>/4-glossary.md` exists (needed for scope-fence lookup).
- `kb/patterns/artifact-impact-matrix.md` exists.
- The caller passes a non-empty `change_summary`.

## Inputs

| Name                       | Shape                                          | Notes                                      |
|----------------------------|------------------------------------------------|--------------------------------------------|
| `change_summary`           | 1–3 sentence prose                              | What the user wants to change, in plain English. When Phase 0 ran, this is the user-confirmed `change_summary` synthesised at Phase 0 exit — treat it as authoritative. |
| `target_artifact_type`     | enum                                           | One of: `cynefin`, `event-storm`, `stories`, `example-map`, `glossary`, `bc-candidates`, `bc-canvas`, `context-map`, `adr`, `plan`, `diagram`. |
| `slug`                     | kebab-case string                              | Which project the revision lives under.    |
| `target_edits_pre_applied` | YAML list (optional)                           | Present when Phase 0 Shape ran. Each entry: `{section, before_excerpt, after_excerpt, angle_id}`. These edits are **already applied** to the target; triage must treat them as facts when extracting `affected_names`. |

## Execution

1. **Read the matrix.** Open `kb/patterns/artifact-impact-matrix.md` and
   hold the change-kind definitions + the matrix table in working memory.

2. **Read the scope fences.** Open
   `projects/<slug>/4-glossary.md` and locate the Renaming Manifest's
   **Scope Fences** subsection (`### Scope Fences`). Record the `frozen`
   and `deferred` file lists — these are excluded from downstream greps.

3. **Classify the change kind.** Apply the taxonomy from the matrix:
   - `cosmetic` if the change does not alter semantics (typo, wording,
     example reorder, clarifying note that preserves meaning).
   - `structural` if the change alters the artifact's own semantics but
     does **not** rename a shared term, change an event/command name,
     redraw a BC boundary, or re-label a context-map edge.
   - `cascading` if the change touches any shared name, BC boundary, or
     edge. **Default to `cascading` when in doubt** — over-reporting in
     triage is cheap; under-reporting causes silent drift.

4. **Extract affected names.** Parse `change_summary` for identifiers that
   may be referenced elsewhere:
   - Glossary terms (capitalised nouns matched by a Grep for exact term in
     `4-glossary.md` definitions).
   - Event/command names (CamelCase or PascalCase tokens ending in a verb
     past-participle or imperative).
   - BC names (known via `5-bc-candidates.md` and `6-contexts/`).
   - Edge labels (relationship types from `7-context-map.md`).
   For each, note whether the change is `rename` (old → new), `add`
   (introduced), `remove` (dropped), or `redefine` (same name, new
   semantics). Redefines are the most dangerous — they silently change
   meaning behind a stable identifier.

   **When `target_edits_pre_applied` is provided (Phase 0 ran):** extract
   affected names by comparing each `before_excerpt` to its
   `after_excerpt` directly — you don't need to infer from prose. Names
   appearing in `before` but not `after` are `remove` ops; names in
   `after` but not `before` are `add` ops; renames are pairs that match
   by surrounding context. This is more reliable than prose parsing when
   Phase 0 output is available. The `change_summary` still matters for
   rationale + Triage kind classification, but the name set is anchored
   in the concrete edits.

5. **Consult the matrix row for `target_artifact_type`.** Record the
   downstream artifact types and their default priorities — this becomes
   `matrix_downstream_types` in the output.

   **Diagrams as a downstream type.** If the matrix row includes
   `0-diagrams/*` (per `kb/patterns/artifact-impact-matrix.md § Diagrams
   as a downstream type`), record the per-tier-ID list (e.g.
   `[T1.4, T2.2, T2.3, T3.1]` for an event-storm change) as a separate
   `diagrams_potentially_affected` field. The actual affected set is
   computed in Phase 3 by greping the project's `0-diagrams/` files for
   prose-source back-links — not all diagrams listed in the matrix row
   may exist in the project. If `0-diagrams/` does not exist or is
   empty, set `diagrams_potentially_affected: []`.

6. **Suggest a scope fence treatment.** For each `affected_name` that
   matches a scope-fence entry (e.g. an external API name also referenced
   in a frozen research doc), note the suggested treatment:
   - `in-sweep` — include in Phase 3 grep.
   - `external-only` — name also refers to an external API; exclude
     frozen-file hits; still sweep in-scope artifacts.
   - `defer` — change affects only deferred files; propose a separate
     dedicated revision later.

## Output

**YAML block, printed to the calling context** (no file writes). Shape:

```yaml
triage:
  kind: cascading              # cosmetic | structural | cascading
  target_artifact: 4-glossary.md
  change_summary: |
    Rename glossary term "Match-Decision-Log" to "Qualification-Decision-Log"
    across all downstream artifacts.
  affected_names:
    - name: Match-Decision-Log
      op: rename
      new_name: Qualification-Decision-Log
      scope_fence: in-sweep
    - name: match-decision-log
      op: rename
      new_name: qualification-decision-log
      scope_fence: in-sweep
      note: "lower-case prose form; sweep separately for readability"
  matrix_downstream_types:
    - 5-bc-candidates.md
    - 6-contexts/*.md
    - 7-context-map.md
    - 7-adr/*
  rationale: |
    target_artifact = glossary → CRITICAL priority row in matrix.
    Change op = rename on a shared term → kind = cascading.
  citations:
    - kb/patterns/artifact-impact-matrix.md § Matrix
    - projects/<slug>/4-glossary.md § Scope Fences
```

## Interrogator behaviours

- **Ambiguous change summary** (e.g. "fix the canvas" without detail) →
  refuse to triage; return a `kind: unknown` and ask the orchestrator to
  clarify with the user before re-invoking.
- **`cosmetic` on a glossary term** → suspicious. A wording change to a
  glossary definition is rarely cosmetic — it usually redefines the term.
  Default to `structural` at minimum; recommend the orchestrator push back.
- **`structural` claimed but the change touches an event name** → override
  to `cascading`. Event names are shared names.
- **No affected names extracted but kind is `cascading`** → self-
  contradiction; return `kind: unknown` and request clarification.
- **Change summary references a frozen-file path directly** → flag as
  out-of-scope; kind = `cosmetic` with `note` explaining why.

## What this skill never does

- Does not write any files.
- Does not invoke other skills or agents.
- Does not apply the change.
- Does not run the downstream grep — that is the `impact-analysis` skill's
  job.

## References

- `kb/patterns/artifact-impact-matrix.md` — change-kind taxonomy; matrix.
- `CLAUDE.md §6` — revision enforcement protocol.
- `kb/canonical/fowler-ubiquitous-language.md` — why glossary changes are
  CRITICAL by default.
