---
name: revision-plan
description: Phase 4 of /ddd:revise. Draft per-artifact patches from the impact analysis + original change summary; write the draft 8-revisions/NNNN-<title>.draft.md skeleton (or extend it if Phase 0 Shape already created one). Skips drafting patches for target-artifact sections that Phase 0 already applied. Writes only to the draft file — no artifact edits.
argument-hint: <impact_analysis_yaml> <change_summary> <slug> [--target-edits-pre-applied <yaml>]
allowed-tools: Read, Grep, Glob, Write, Edit
---

# Revision Plan

Phase 4 skill for `/ddd:revise`. Takes the impact analysis (Phase 3) and
the original change summary, reads each impacted artifact's flagged
sections, drafts a concrete prose patch per section, and writes a
**draft** `projects/<slug>/8-revisions/NNNN-<title>.draft.md` record.

The draft is **not** an applied patch. Phase 5 walks the user through each
patch with `AskUserQuestion`; only after user approval does the orchestrator
apply patches and rename the draft to drop the `.draft` suffix.

**Canonical reference:** `kb/patterns/artifact-impact-matrix.md`.

## Preconditions

- Phase 3 has produced an impact YAML with at least one `impacted:` entry
  (or, for `cosmetic`, an empty list — in which case this skill emits a
  minimal record and returns).
- `projects/<slug>/8-revisions/` exists or can be created.
- The caller passes the original `change_summary` and `triage_output` so
  the draft record is self-contained.

## Inputs

| Name                         | Shape                          | Notes                                              |
|------------------------------|--------------------------------|----------------------------------------------------|
| `impact_analysis_yaml`       | YAML block from `impact-analysis` | Contains `impacted:` list.                         |
| `change_summary`             | 1–3 sentence prose              | The original user-stated intent (or Phase 0 synthesis). |
| `triage_output_yaml`         | YAML block from `revision-triage` | Needed for the record's header section.           |
| `slug`                       | kebab-case string              | Which project.                                     |
| `target_edits_pre_applied`   | YAML list (optional)           | Present when Phase 0 Shape ran. Each entry: `{section, before_excerpt, after_excerpt, angle_id}`. Phase 4 must **skip** drafting patches for any impacted entry whose `{artifact, section}` tuple matches an entry here — otherwise Phase 5 would re-apply the same edit. |

## Execution

### 1. Resolve the revision number (or reuse a Phase 0 draft)

1. **Check for an existing Phase 0 draft.** Glob
   `projects/<slug>/8-revisions/*.draft.md`. If exactly one draft exists
   and its `Status:` line reads `Draft — Phase 0 Shape in progress` or
   it contains a `## Phase 0 Shape Summary` section but no `## Triage`
   section, **reuse** that file:
   - Extract the NNNN and title-slug from the filename.
   - Extend the file in-place with the Phase 4 sections below the
     existing Phase 0 Shape Summary (via `Edit`, not `Write`).
   - Update the `Status:` line to
     `Draft — Phase 4 plan complete, awaiting Phase 5 walk`.
   Skip the rest of step 1.
2. **Otherwise, allocate a new record.** Glob
   `projects/<slug>/8-revisions/*.md` (both `.draft.md` and `.md`).
   Parse the numeric prefix of each (e.g. `0001`, `0002`). Next number
   = max + 1, zero-padded to 4 digits. If no files exist, start at
   `0001`.
3. Title-slug: derive from `change_summary` — first 6 significant words,
   kebab-case, ASCII-only.

### 2. For each impacted artifact, draft a patch

For each entry in `impact_analysis_yaml.impacted[]` where `deferred: false`:

1. **Skip-check for Phase 0 pre-applied edits.** If
   `target_edits_pre_applied` is present, check whether the current
   `{artifact, section}` tuple matches any entry in that list. If it
   matches:
   - **Skip** drafting a patch for this entry.
   - Record the entry in a new `### Target edits applied in Phase 0
     Shape (no Phase 5 walk needed)` subsection of the draft record,
     citing the `angle_id` and the Before/After excerpts from
     `target_edits_pre_applied`.
   - Increment `target_edits_skipped` for the output summary.
   - Proceed to the next entry. Do **not** re-verify the edit (Phase 0
     already verified it during `apply` mode).
2. Read the artifact file.
3. For each `section` in the entry, read just that section (from the
   `^## ` or `^### ` heading to the next same-or-higher heading).
4. Draft a **prose patch** (not a raw diff). Structure:
   - **Before (excerpt):** 2–6 lines quoting the current content.
   - **After (proposed):** the proposed replacement.
   - **Rule applied:** cite the source of the rule (e.g.
     `4-glossary.md § Renaming Manifest row N`, or
     `kb/canonical/fowler-ubiquitous-language.md`).
   - **Rationale:** 1 sentence on why this patch is needed.
5. For entries where `deferred: true`: do **not** draft a patch. Record
   the entry in the `deferred_patches:` section of the record with a
   note that a dedicated revision should be scheduled.
6. For entries with `coverage_warning: true`: still draft the patch, but
   flag it for extra scrutiny in Phase 5.

### 3. Check for patch interactions

After drafting all patches, scan for cross-patch conflicts:

- **Two patches touching the same line** → merge into one patch; flag
  `conflict: merged` in the record.
- **A patch that renames a producer and another patch that updates the
  subscriber** → confirm both patches reference the new name consistently;
  flag `linked_patches: [M, N]`.
- **A patch that would contradict an existing Business Decision or ADR**
  → flag `interrogator_required: true` on that patch. The `ddd-reviser`
  agent will route to `ddd-interrogator` before presenting to the user.

### 4. Write the draft record

Write `projects/<slug>/8-revisions/<NNNN>-<title-slug>.draft.md` with
the structure below. **This is the only file this skill writes.**

### 5. Emit pointer

Return the path to the draft file + a brief YAML summary:

```yaml
plan:
  draft_record: projects/<slug>/8-revisions/0001-rename-match-decision-log.draft.md
  draft_record_reused: false   # true when Phase 0 created the file and Phase 4 extended it
  patch_count: 4
  interrogator_required_count: 1
  deferred_count: 1
  conflicts_merged: 0
  target_edits_skipped: 0      # count of impacted entries skipped because Phase 0 already applied them
```

## Draft record structure

```markdown
# Revision NNNN — <title>

**Status:** Draft (Phase 4 of /ddd:revise)
**Date:** YYYY-MM-DD
**Slug:** <slug>
**Kind:** cosmetic | structural | cascading
**Operator:** <user>

## Trigger

<change_summary verbatim>

## Triage

- **Target artifact:** <target_artifact>
- **Change kind:** <kind>
- **Affected names:** <list>
- **Matrix row consulted:** <link>

## Impact analysis

**Sweep scope:** in_sweep=<N>, deferred=<N>, frozen_excluded=<N>.

| # | Artifact | Section(s) | Priority | Deferred | Interrogator |
|---|----------|------------|----------|----------|--------------|
| 1 | <path> | <§> | HIGH | no | no |
| 2 | ... | | | | |

## Proposed patches

### Patch 1 — <artifact> § <section> [priority: HIGH]

**Before:**
```
<quoted excerpt>
```

**After:**
```
<proposed replacement>
```

**Rule applied:** <citation>
**Rationale:** <one sentence>

### Patch 2 — ...

## Deferred patches (scheduled, not applied)

- `<artifact>` — reason: in deferred scope-fence (<manifest row link>);
  recommend dedicated `/ddd:revise` at <trigger condition>.

## Sweep verification (to be filled by Phase 5)

- [ ] Drift sweep run for each `affected_name` across in-sweep artifacts.
- [ ] Zero unpatched drift confirmed.
- [ ] Renaming Manifest row added (if `kind: cascading`).
- [ ] Glossary-curator invoked (if any term touched).

## Operator verification

- [ ] Operator reviewed all applied patches.
- [ ] Operator confirms deferred patches are correctly scoped.
- [ ] Operator signs off (initials + date).

## References

- `kb/patterns/artifact-impact-matrix.md`
- `CLAUDE.md §6`
- <other citations>
```

## Interrogator behaviours

- **A proposed patch rewrites a Renaming Manifest row** → flag
  `interrogator_required: true`. Manifest rows are load-bearing; changes
  need Plöd-L3 review.
- **A proposed patch would leave a dangling reference** (the "After"
  removes a name referenced elsewhere not in the impact list) → abort
  patch drafting for that entry, emit a new triage request with the
  newly-discovered name, and ask the orchestrator to re-run Phase 3.
- **Two patches contradict each other** (e.g. one adds a term, another
  renames it) → flag as `contradiction`; orchestrator must route to
  interrogator before Phase 5.
- **Patch touches an ADR marked Accepted** → draft the patch, but flag
  `supersede_candidate: true`; the appropriate action may be to write a
  new ADR that supersedes the old one rather than edit the old one in
  place.

## What this skill never does

- Does **not** apply patches to artifact files. Patches live in the draft
  record only until Phase 5 approves them.
- Does not invoke AskUserQuestion, Agent, Skill, or SendMessage.
- Does not run the drift-sweep grep — that's Phase 5 verification.
- Does not modify the Renaming Manifest.

## References

- `kb/patterns/artifact-impact-matrix.md` — matrix + change-kind taxonomy.
- `CLAUDE.md §6` — revision enforcement protocol; manifest schema.
- `projects/charge-relay/4-glossary.md § Renaming Manifest` — reference
  manifest implementation to mirror schema.
