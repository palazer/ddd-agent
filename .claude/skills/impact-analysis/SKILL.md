---
name: impact-analysis
description: Phase 3 of /ddd:revise. Given triage output + slug, walk the artifact-impact matrix and run deterministic greps across projects/<slug>/ to produce a ranked list of candidate-impacted artifacts with section-level citations. Pure compute — no writes.
argument-hint: <triage_output_yaml> <slug>
allowed-tools: Read, Grep, Glob
---

# Impact Analysis

Phase 3 skill for `/ddd:revise`. Consumes the YAML output of the
`revision-triage` skill, walks the dependency matrix, runs deterministic
greps for every `affected_name`, honours scope fences, and returns a
ranked list of candidate-impacted artifacts for the user's Phase 3
checkpoint.

**Canonical reference:** `kb/patterns/artifact-impact-matrix.md`.

## Preconditions

- Phase 2 triage has run and produced a YAML block with `kind`,
  `affected_names`, and `matrix_downstream_types`.
- `projects/<slug>/` exists and contains at least the artifacts named in
  `matrix_downstream_types`.
- `kb/patterns/artifact-impact-matrix.md` is readable.
- `projects/<slug>/4-glossary.md` Renaming Manifest scope fences are
  readable.

## Inputs

| Name                  | Shape                          | Notes                                                 |
|-----------------------|--------------------------------|-------------------------------------------------------|
| `triage_output_yaml`  | YAML block from `revision-triage` | Must include `kind`, `affected_names`, `matrix_downstream_types`. |
| `slug`                | kebab-case string              | Which project.                                         |

## Execution

### 0. Short-circuit for `cosmetic`

If `triage.kind == cosmetic`, return an empty `impacted:` list
immediately with a note. No grep work is needed.

### 1. Resolve the sweep scope

For the given `slug`:

1. Glob `projects/<slug>/*.md` and `projects/<slug>/**/*.md` to get the
   full candidate file list.
2. Exclude:
   - `projects/<slug>/input/**` (always read-only).
   - Files listed under `### Scope Fences` → `**Frozen**` in the Renaming
     Manifest.
3. Separate the remaining files into:
   - `in_sweep` — normal targets.
   - `deferred` — files listed under `### Scope Fences` → `**Deferred**`.
     Grep these, but in the output mark them as `deferred: true` so the
     `ddd-reviser` agent skips patching them and logs them as open
     follow-ups.

### 2. Grep for each affected name

For each entry in `triage.affected_names`:

1. Build a Grep pattern using the **negative-lookbehind trick** proven
   out in the 2026-04-21 charge-relay migration:
   ```
   (?<![-0-9])<literal-name>(?![-0-9])
   ```
   The guard prevents matches inside numbered filenames (e.g. a search
   for `contexts/` should not match `6-contexts/`) and inside
   alphanumerically-adjacent identifiers.
2. Run `Grep` with `output_mode: "content"` and `-n: true` across the
   `in_sweep` + `deferred` file lists. Record each hit as
   `(file, line_number, line_text)`.
3. For each hit, heuristically identify the **section** — walk backwards
   from the hit line to the nearest `^## ` or `^### ` heading in the same
   file. Record the heading text as `section`.

### 3. Assign priority

For each `(file, section)` tuple (deduplicated across hit lines), assign a
default priority by cross-referencing the `matrix` table in
`kb/patterns/artifact-impact-matrix.md`:

- Look up the row for `triage.target_artifact_type`.
- Find the column entry that matches the `file`'s artifact type.
- Use the priority stated in the matrix (`CRITICAL | HIGH | MEDIUM | LOW`).
- If the file is `deferred`, cap the priority at `MEDIUM` and add
  `deferred: true` to the entry.

### 4. Cross-check sections against the matrix mechanism column

If the matrix says "Inbound/outbound ↔ map edges" for the row, but no hit
appears in the canvas's § 5 (Inbound Communication) or § 6 (Outbound
Communication), flag `coverage_warning: true` on that entry — the user
should decide whether the absence is real or if the grep missed the hit
for a naming-variant reason.

### 5. Sort and emit

Sort the final `impacted:` list by `priority` (CRITICAL > HIGH > MEDIUM >
LOW), then alphabetically by file. Emit as YAML (see Output below).

## Output

**YAML block, printed to the calling context** (no file writes). Shape:

```yaml
impact:
  slug: charge-relay
  triage_ref: |
    kind=cascading, target=4-glossary.md, names=[Match-Decision-Log]
  sweep_scope:
    in_sweep_count: 37
    deferred_count: 4
    frozen_count_excluded: 2
  impacted:
    - artifact: projects/charge-relay/6-contexts/offer-qualification.md
      sections:
        - "7. Ubiquitous Language"
        - "8. Business Decisions"
      hits: 6
      reason: "cites renamed term 'Match-Decision-Log' in UL + Decision 4"
      priority: HIGH
      deferred: false
      coverage_warning: false
    - artifact: projects/charge-relay/7-context-map.md
      sections:
        - "Edge 3 (OMS ↔ Reconciliation)"
      hits: 1
      reason: "edge label uses renamed term"
      priority: HIGH
      deferred: false
      coverage_warning: false
    - artifact: projects/charge-relay/7-adr/0004-match-decision-log.md
      sections:
        - "Title"
        - "Decision"
        - "Consequences"
      hits: 11
      reason: "ADR is titled after the renamed term; all sections reference it"
      priority: MEDIUM
      deferred: false
      coverage_warning: false
    - artifact: projects/charge-relay/domain-model.md
      sections:
        - "Entities"
      hits: 3
      reason: "term referenced in legacy solution doc"
      priority: MEDIUM
      deferred: true
      coverage_warning: false
  citations:
    - kb/patterns/artifact-impact-matrix.md § Matrix
    - projects/charge-relay/4-glossary.md § Scope Fences
```

## Interrogator behaviours

- **Zero hits for a `cascading` rename** → suspicious. The renamed term
  should be referenced *somewhere* downstream. Return a
  `coverage_warning: true` on the whole analysis and recommend the user
  re-check the spelling or variant forms.
- **Hits only in `frozen` files** → the change is entirely external-
  facing; return empty `impacted:` and a note recommending
  `kind: cosmetic` reclassification in Phase 2.
- **Hits in `input/`** → this is a bug in scope resolution. Abort and
  surface an error; `input/` must never be swept.
- **> 50 impacted artifacts from a single change** → the revision is too
  large for one `/ddd:revise` session. Recommend the user split the
  change into multiple revisions, ordered by priority.

## What this skill never does

- Does not write any files.
- Does not propose patches — that is the `revision-plan` skill's job.
- Does not apply the change.
- Does not modify the Renaming Manifest.

## References

- `kb/patterns/artifact-impact-matrix.md` — matrix + scope fences.
- `CLAUDE.md §6` — revision enforcement protocol.
- `projects/charge-relay/4-glossary.md § Drift Sweep Log` — empirical
  evidence that the negative-lookbehind grep pattern works at scale (223
  references rewritten across 35 files with 0 false positives on
  2026-04-21).
