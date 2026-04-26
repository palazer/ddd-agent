---
name: revision-shape
description: Phase 0 of /ddd:revise. Co-shape a revision with the user through interrogator-style dialog — survey the target artifact for refinement-worthy spots, draft Before/After edits, and apply them in place. Runs before Phase 1 Capture. Does not sweep downstream, does not classify kind, does not write Manifest rows.
argument-hint: --mode <survey|draft|apply> --target <path> [--slug <slug>] [--focus <angle-id>] [--edit-spec <yaml>]
allowed-tools: Read, Grep, Glob, Edit
---

# Revision Shape

Phase 0 skill for `/ddd:revise`. Given a target artifact the user wants
to refine (but has not yet decided how), this skill runs in three modes
driven by the `ddd-reviser` agent:

- `survey` — read the target, detect refinement-worthy spots, return a
  YAML list of candidate angles.
- `draft` — given a focus angle (or an explicit user instruction), return
  a Before/After edit proposal as YAML.
- `apply` — given an approved edit spec, apply it to the target artifact
  via `Edit`.

The skill is **stateless between calls**. The reviser agent owns the
conversational loop, the AskUserQuestion gates (`Work this angle` /
`Propose something else` / `Edit a specific section` / `Pause and save`
on survey; `Apply` / `Refine` / `Hold` on draft), and the in-memory Phase
0 buffer that eventually flushes to the `## Phase 0 Shape Summary`
section of the numbered draft record.

**Canonical references:**
- `kb/patterns/artifact-impact-matrix.md` — artifact taxonomy + why
  certain edits have downstream priority (informs angle ranking, not
  sweep).
- `kb/patterns/cynefin-when-to-ddd.md` — Plöd-L3 ceiling; the skill
  never decides *for* the user.
- `kb/voices/baas-schwegler-facilitation.md` — preserve dissent / never
  silently close hotspots.
- `kb/anti-patterns/premature-tactical-design.md` — refuse if user pushes
  toward aggregates, schemas, framework code.

## When this skill runs

- Invoked by `ddd-reviser` in Phase 0, after Shape C (`--shape` flag) is
  explicit, or after the reviser detected a no-delta / vague-prose
  invocation and the user said `Yes, shape with me`.
- Never invoked by `/ddd:bc-canvas`, `/ddd:glossary`, or any forward
  ceremony.
- Always stateless: a fresh `Skill` call per mode transition.

## Preconditions

- `target_artifact` path exists under `projects/<slug>/` and is not
  declared `frozen` in `projects/<slug>/4-glossary.md § Scope Fences`.
- If the artifact path falls under `projects/<slug>/input/`, refuse and
  return an error (input is read-only per CLAUDE.md).
- `kb/patterns/artifact-impact-matrix.md` is readable.

## Inputs

| Name            | Shape                                       | Required in mode   |
|-----------------|---------------------------------------------|--------------------|
| `mode`          | `survey` \| `draft` \| `apply`              | all                |
| `target`        | path to the target artifact                 | all                |
| `slug`          | kebab-case project slug                     | survey, draft      |
| `focus`         | `angle_id` from a prior survey output, or a user-typed description of what to work on | draft |
| `edit_spec`     | YAML block produced by the `draft` mode; the user-approved proposal | apply |

## Mode — `survey`

Goal: read the target artifact, detect refinement-worthy spots, return a
ranked list of candidate angles the user can pick from.

### Steps

1. **Read the target.** `Read` the full artifact. If > 2000 lines, warn
   the orchestrator; this skill is not intended for mega-artifacts.
2. **Identify the artifact type** from the path:
   - `6-contexts/<bc>.md` → Bounded Context Canvas (11 sections).
   - `5-bc-candidates.md` → BC candidate list.
   - `7-context-map.md` → Context Map (edges + patterns).
   - `7-adr/NNNN-*.md` → ADR.
   - `4-glossary.md` → Glossary.
   - `2-event-storm.md` → Event Storm.
   - `3-domain-stories.md`, `3b-example-maps/*.md` → stories / examples.
3. **Run type-specific heuristic scans** to detect spots worth refining.
   (Exact heuristics per artifact type below.)
4. **Score and rank** candidates. Sorting rule: artifact-impact-matrix
   priority of the affected name/section (CRITICAL > HIGH > MEDIUM > LOW)
   breaks ties toward higher impact, but the user still picks; the skill
   does not filter anything out at this stage.
5. **Emit YAML** with 3–5 angles (more if the artifact has many hotspots;
   cap at 7 to keep the user's AskUserQuestion readable).

### BC Canvas heuristics (`6-contexts/<bc>.md`)

Scan each of the 11 sections and surface:

- **§2 Purpose — vague prose.** Purpose section contains buzzwords only
  (`"enables"`, `"supports"`, `"drives"` with no concrete verb-object
  pair); no named actors; no reference to a Core/Supporting
  classification.
- **§4 Domain Roles — conflicting archetypes.** Two or more archetypes
  claimed without a primary; or one archetype claimed while §6 Business
  Decisions hint at a different one (e.g. archetype = Specification but
  §6 has execution-flavour decisions).
- **§5 Ubiquitous Language — orphan terms in §6/§8.** A term appears in
  §6 Business Decisions or §8 Outbound Events without an entry in §5.
- **§6 Business Decisions — rows without an ADR.** A decision row claims
  a binding policy but no `7-adr/NNNN-*.md` is referenced. Candidate
  for promotion to ADR.
- **§7 Inbound / §8 Outbound — events not in the Event Storm.** An event
  name in §7/§8 that doesn't appear in `2-event-storm.md`. Candidate for
  either adding to storm or re-labelling to an existing one.
- **§9 Policies — policy stated as narrative, not as a rule.** Policy
  section is prose without `Given/When/Then` or explicit state-machine
  transitions.
- **§10 Anti-Patterns — unchecked hotspots.** A hotspot flagged but no
  mitigation text; or mitigation cites no `kb/` path.
- **§11 Open Questions — pile-up.** More than 3 open questions, or any
  open question cross-referenced from another artifact
  (`7-adr/` or another canvas) still unresolved.
- **§12 Horizon Notes — H1/H2 boundaries drifting.** An item appears in
  both H1 and H2 scope without an explicit handoff mechanism; or a
  "deferred to H2" item lacks a stub contract.

### BC candidates heuristics (`5-bc-candidates.md`)

- A candidate without an archetype.
- A candidate claimed as Core but without a differentiation rationale.
- Two candidates with overlapping responsibilities and no Shared Kernel
  / Anti-Corruption-Layer relationship recorded.

### Context Map heuristics (`7-context-map.md`)

- An edge named but missing an organisational relationship
  (P/CS/CF) **or** an integration pattern (SK/OHS/PL/ACL/CF/SW).
- An edge that appears in one canvas's §8 Outbound but not in the map.
- A BC with zero edges.

### ADR heuristics (`7-adr/NNNN-*.md`)

- Status == Proposed but no date or no `Consequences` section.
- `Decision` references a term not in `4-glossary.md`.
- `Supersedes` row points at an ADR that doesn't exist in the project.

### Glossary heuristics (`4-glossary.md`)

- Term defined with a synonym-chain ("see also X", "also known as Y")
  that isn't resolved on either side.
- Term defined with prose borrowing external API grammar without a
  renamed-from row in the Manifest.
- A term referenced in `5-bc-candidates.md` or any `6-contexts/*.md`
  without a glossary entry.

### Output — `survey` mode YAML

```yaml
shape:
  mode: survey
  target: projects/<slug>/6-contexts/offer-authoring.md
  artifact_type: bc-canvas
  angles:
    - id: A1
      section: "§11 Open Questions"
      kind: "pile-up"
      one_line: "Six open questions; Q1 (versioning) and Q3 (distribution authoring) have downstream impact on OPS + Distribution BCs."
      impact_hint: HIGH
      heuristic: "open-questions-pile-up"
    - id: A2
      section: "§6 Business Decisions"
      kind: "orphan-ADR-candidate"
      one_line: "BD-4 ('co-funded offer budget exhaustion policy') is stated as a binding policy but not promoted to an ADR."
      impact_hint: MEDIUM
      heuristic: "bd-without-adr"
    - id: A3
      section: "§5 Ubiquitous Language"
      kind: "orphan-term"
      one_line: "'OfferCapsUpdated' appears in §8 Outbound events but not in §5 glossary."
      impact_hint: HIGH
      heuristic: "outbound-event-not-in-ul"
    # ...
  notes:
    - "Survey is not a triage. The user picks the angle; kind classification happens in Phase 2."
  citations:
    - kb/patterns/artifact-impact-matrix.md § Priorities
    - kb/voices/baas-schwegler-facilitation.md § Preserve dissent
```

## Mode — `draft`

Goal: given a focus angle (from the survey) or a free-form user
instruction, draft a concrete Before/After edit proposal.

### Steps

1. **Read the target section.** `Read` just the section referenced by
   the focus angle (from the `^## ` or `^### ` heading to the next
   same-or-higher heading).
2. **Compose a Before/After.**
   - **Before:** 3–8 lines quoting the current content verbatim.
   - **After:** the proposed replacement; prose reads as if it were
     going to live in the artifact.
   - **Rationale:** one sentence; cite the heuristic and any `kb/` path.
   - **Sibling-artifact hint:** if the proposed edit would obviously
     cascade (e.g. adds a new event → Event Storm should learn about it;
     adds a new term → glossary needs an entry), **list the sibling
     artifacts as *hints*** but do **not** draft patches for them.
     Those are Triage/Impact/Plan's job.
3. **Flag interrogator review** if the edit touches:
   - BC boundaries (§2 Purpose rewrite, §4 Archetype change,
     §9 Policy rewrite that changes state-machine transitions).
   - ADR decisions (edits under `7-adr/`).
   - Business Decision rows (§6) with a binding-policy clause.
   - Renaming Manifest rows.
   - §5 or §7 (Ubiquitous Language / Inbound) term redefinitions.

### Output — `draft` mode YAML

```yaml
shape:
  mode: draft
  target: projects/<slug>/6-contexts/offer-authoring.md
  focus: A1
  section: "§11 Open Questions"
  before: |
    1. **Offer versioning strategy:** When an existing offer needs amendment (e.g., budget increase mid-campaign), do we:
       - **(A)** Edit in-place and emit OfferAmended event?
       - **(B)** Cancel old offer, create new offer with updated terms, and issue refund/credit for partial budget?
       - **Recommendation:** (B) — immutable; cleaner audit trail. Deferred to ADR (Step 7).
  after: |
    1. **Offer versioning strategy — RESOLVED (see ADR-0004).** Offers are immutable once ACTIVE; amendments require Cancel-and-Reissue. Partial-budget refund is out-of-band (bilateral). The `OfferAmended` event is retired from consideration.
  rationale: "Promote BD-implicit resolution from recommendation to ADR-backed policy; removes a pile-up item and gives OPS a stable contract."
  citations:
    - kb/patterns/artifact-impact-matrix.md § ADR priority
  interrogator_required: true
  interrogator_reason: "Edit introduces a new ADR reference (ADR-0004) that does not yet exist; also redefines §9 Policy lifecycle wording (immutable once ACTIVE)."
  sibling_hints:
    - "7-adr/ — a new ADR-0004 should be authored; recommend a dedicated ADR ceremony after Phase 0 closes."
    - "5-bc-candidates.md — OPS shared-kernel section should reflect the new stable contract."
```

The `sibling_hints` field is **informational only.** The reviser agent
records hints in the Phase 0 buffer but does NOT act on them during
Phase 0. Triage/Impact phases (2–3) decide the actual downstream scope.

## Mode — `apply`

Goal: given an approved `edit_spec` YAML (from `draft` mode, after the
user picked `Apply` in the reviser's gate), apply the edit to the target.

### Steps

1. **Read the target file** to confirm the `before` text still matches
   verbatim. If the file changed under the reviser's feet, abort with an
   error — the reviser should re-draft.
2. **Invoke `Edit`** with `old_string = before`, `new_string = after`.
   `replace_all: false`. If the `before` string is not unique in the
   file, abort; the reviser re-drafts with a wider excerpt.
3. **Post-apply verify** by `Read`-ing the section back and confirming
   the `after` text is now present and the `before` text is absent.

### Output — `apply` mode YAML

```yaml
shape:
  mode: apply
  target: projects/<slug>/6-contexts/offer-authoring.md
  applied: true
  section: "§11 Open Questions"
  focus: A1
  verified: true
  lines_changed: 7
  notes:
    - "Target artifact edit applied. Phase 0 buffer should record this as one entry in the 'Edits applied' list."
```

If `applied: false` (e.g. uniqueness failure, text drift), the YAML
includes an `error:` field and the reviser should re-run `draft` mode.

## Interrogator hand-off (reviser's responsibility, noted here for
completeness)

The skill **flags** `interrogator_required: true` in `draft` output. The
reviser agent is responsible for routing to `ddd-interrogator` BEFORE
presenting the draft to the user via `AskUserQuestion`. The skill itself
does not invoke `Agent` or `SendMessage` — state carries through the
reviser.

Rules (per `CLAUDE.md §1` Plöd-L3 ceiling):

- Spawn-once, resume-thereafter. First interrogator call in the session
  → `Agent`; subsequent calls → `SendMessage`. Same pattern as the
  existing Phase 4 pre-walk hook in `agents/ddd-reviser.md`.
- Cap at 2 rounds per patch. On round 3 → reviser escalates to user
  with a `Hold` recommendation.

## Phase 0 exit discipline (non-negotiable — enforced by the reviser)

The skill supports these invariants by *never* doing the following, in
any mode:

- Never reads or edits any artifact other than the `target` (and its
  glossary for scope-fence lookup in survey). Sibling artifacts are
  mentioned in `sibling_hints` only.
- Never writes to `4-glossary.md § Renaming Manifest` — that's Phase 5.
- Never writes to `4-glossary.md § Drift Sweep Log` — that's Phase 5.
- Never classifies the change as `cosmetic | structural | cascading` —
  that's Phase 2 (Triage).
- Never runs `Grep` across the project for the purpose of propagation —
  only for local heuristic detection within the target.
- Never emits a `change_summary`. The reviser synthesises the
  `change_summary` from the Phase 0 buffer at Phase 0 exit (when the
  user picks `Proceed to Triage`).

## Anti-patterns to refuse

- **Tactical-code drift.** If the user's focus angle asks for code
  (aggregate skeletons, schema DDL, framework code) → refuse per
  `CLAUDE.md §3` and `kb/anti-patterns/premature-tactical-design.md`.
- **Silent-resolve of a hotspot.** If the focus angle is "remove this
  open question without resolution" → require a stated rationale in the
  `after` text; cite `kb/voices/baas-schwegler-facilitation.md`.
- **Manifest-row rewrite attempted inside Phase 0.** Refuse and tell
  the reviser to defer to Phase 5.

## What this skill never does

- Does not invoke `AskUserQuestion`, `Agent`, `SendMessage`, or any
  orchestration tool. The reviser agent owns the loop.
- Does not invoke other skills.
- Does not write to any file other than via `Edit` on the `target` in
  `apply` mode.
- Does not run downstream artifact scans. The survey is target-local.

## References

- `kb/patterns/artifact-impact-matrix.md` — artifact taxonomy; priority.
- `kb/patterns/cynefin-when-to-ddd.md` — Plöd-L3 ceiling.
- `kb/voices/baas-schwegler-facilitation.md` — preserve dissent.
- `kb/anti-patterns/premature-tactical-design.md` — refuse code drift.
- `CLAUDE.md §1, §3, §4, §6` — hard rules honoured by this skill.
- `agents/ddd-reviser.md` — the orchestrator that drives the
  Phase 0 loop.

## Attribution

Structure adapted from `skills/event-storming/SKILL.md`
(interrogator-mode facilitation, hotspot preservation) and
`skills/revision-plan/SKILL.md` (Before/After patch drafting,
interrogator-required flagging). Multi-mode invocation pattern is novel
to this skill — necessary because Phase 0 is a conversational loop
rather than a single-shot compute.
