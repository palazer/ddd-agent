---
name: event-storming
description: Facilitate a Big-Picture Event Storming session for ideation. Interrogator-mode — surfaces past-tense events, actors, external systems, hotspots, and pivotal events, without deciding anything for the user. Writes outputs to projects/<slug>/2-event-storm.md.
argument-hint: <domain-description> [--slug <project-slug>]
allowed-tools: Read, Write, Edit, Glob, Grep, Skill, Agent
adapted_from: melodic-software/claude-code-plugins — plugins/enterprise-architecture/skills/event-storming (MIT). Structure and phase names retained; multi-persona simulation machinery replaced with single-interrogator Plöd-L3 stance per CLAUDE.md rule #1.
---

# Event Storming (Big-Picture)

Facilitator skill for step 2 of `/ddd:ideate`. Runs a Big-Picture Event Storming
session to discover domain events in past tense, order them chronologically,
attach actors and external systems, and surface hotspots.

**Canonical reference:** `kb/ceremonies/event-storming.md`. This skill executes
the phases defined there; it does **not** add new notation or invent new
facilitation rules.

## When this skill runs

- Invoked by `/ddd:event-storm` directly.
- Invoked by `/ddd:ideate` after Cynefin framing passes (step 2).
- Invoked by a user explicitly asking to run Event Storming on a prompt.

## Preconditions

- A project slug exists (`projects/<slug>/`). If not, create it from the
  description.
- Cynefin framing has been done, or a soft version is inferred from the
  prompt. If the domain is Clear/Simple or Chaotic, **decline** and redirect —
  do not run a full storm. Cite `kb/patterns/cynefin-when-to-ddd.md`.

## Execution — phases from kb/ceremonies/event-storming.md

Run the five phases sequentially. After each phase, **checkpoint with the user**
before proceeding (per Plöd-L3 + 7-gates pattern from
`research/02-toolkit-audit.md`).

### Phase 1 — Chaotic Exploration
Ask the user to brainstorm past-tense events in any order. Do **not** help
invent events; the user (and any domain expert they surface) is the source
of truth. The skill's job is to:

- **Reject** present/future-tense stickies on sight. Cite
  `kb/ceremonies/event-storming.md § The invariant`. Ask for a restatement.
- **Deduplicate** obvious duplicates by stacking.
- **Capture verbatim** what the user says, in the markdown table.

### Phase 2 — Enforce the Timeline
Walk the events left-to-right with the user. Where the user hesitates or two
events seem to compete for the same position → **record a hotspot**. Do not
resolve.

### Phase 3 — People & Systems
For each event: who triggered it (Actor), what external system was involved.
Use only role names (per
`kb/ceremonies/domain-storytelling.md § The process § 3`).

### Phase 4 — Pivotal Events
Ask the user which events represent state transitions the business genuinely
cares about. Mark these. They feed step 5 (Candidate Bounded Contexts).

### Phase 5 — Walk-Through
Tell the story end-to-end. Missing events surface. Disagreements surface.
Preserve both.

## Interrogator behaviours (non-negotiable, from CLAUDE.md)

- **Refuse premature tactical work.** If the user starts naming aggregates,
  entities, or schema → cite `kb/anti-patterns/premature-tactical-design.md`
  and redirect.
- **Refuse non-past-tense events.** Cite `kb/ceremonies/event-storming.md`.
- **Preserve hotspots.** Never resolve by fiat. Cite
  `kb/voices/baas-schwegler-facilitation.md § Preserve dissent`.
- **Limit scope.** Do not run Design-Level or Process-Level storms — those are
  v2. If the user asks, explain the v1 scope and offer Big-Picture only.

## Output artifact — `projects/<slug>/2-event-storm.md`

```markdown
# Event Storm — <project name>

**Session date:** <YYYY-MM-DD>
**Phase reached:** <1/2/3/4/5>  (not all storms finish in one sitting)
**Related kb references:** kb/ceremonies/event-storming.md

## 1. Event Catalog (past tense, timeline order)

| # | Event (past tense) | Actor | External system | Pivotal? |
|---|---|---|---|---|
| 1 | OrderPlaced | Customer | — | ✓ |
| 2 | PaymentAuthorized | — | PaymentGateway | |
| ... | | | | |

## 2. Actor Roster
- **Customer:** triggers events #1, #5
- ...

## 3. External System Roster
- **PaymentGateway:** inbound events #2, #7; outbound —
- ...

## 4. Hotspots (UNRESOLVED — do not silently close)

| # | Near event # | Description | Surfaced by |
|---|---|---|---|
| 1 | 3–4 | Disagreement on whether OrderConfirmed happens before or after PaymentAuthorized | Alice / Bob |
| ... | | | |

## 5. Pivotal Events
- #1 OrderPlaced
- #7 OrderShipped
- ...

## 6. Open Questions (feed to glossary / next step)
- Is "Customer" always end-user, or sometimes B2B account?
- ...
```

## Post-run suggestions

Emit to the user, verbatim:

> **Next recommended step.** Based on the storm:
> - Hotspots about *workflow* → run `/ddd:domain-storytell` for those flows.
> - Hotspots about *rules* → run `/ddd:example-map` for those rules.
> - Pivotal-event clusters are ready → proceed to candidate Bounded Contexts.
> - New terms surfaced → run `/ddd:glossary` to capture them.

## References (from kb/)

- `kb/ceremonies/event-storming.md` — phase definitions, notation, invariants.
- `kb/ceremonies/domain-storytelling.md` — complementary ceremony.
- `kb/ceremonies/example-mapping.md` — rule-level follow-up.
- `kb/anti-patterns/premature-tactical-design.md` — what to push back on.
- `kb/voices/baas-schwegler-facilitation.md` — facilitation stance.
- `kb/voices/tune-strategic-sparring.md` — sparring stance.

## Attribution

Structure adapted from melodic-software/claude-code-plugins
(`plugins/enterprise-architecture/skills/event-storming/`), MIT licence. Phase
names and `references/` pattern retained. The multi-persona simulation
machinery was intentionally removed — this agent prefers a real human in the
loop over simulated consensus (per CLAUDE.md hard rule #1 and
`kb/voices/tune-strategic-sparring.md`).
