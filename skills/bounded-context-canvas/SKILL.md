---
name: bounded-context-canvas
description: Fill or refine a Bounded Context Canvas v5 for one candidate BC. 11 sections — name, purpose, strategic classification, domain roles, inbound/outbound communication, ubiquitous language, business decisions, assumptions, verification metrics, open questions. Writes to projects/<slug>/6-contexts/<bc-slug>.md.
argument-hint: <bc-name> [--slug <project-slug>]
allowed-tools: Read, Write, Edit, Glob, Grep, Skill, Agent
---

# Bounded Context Canvas (v5)

Facilitator skill for step 6 of `/ddd:ideate`. Fills the ddd-crew Bounded
Context Canvas v5 for one candidate BC.

**Canonical reference:** `kb/ceremonies/bounded-context-canvas.md`.

## Preconditions

- `projects/<slug>/5-bc-candidates.md` exists and includes this BC.
- `projects/<slug>/4-glossary.md` exists (may be seed only).
- An event storm exists or the user is prepared to reference one.

## Execution

Walk the **11 sections** in the canonical order, **checkpointing after each**:

1. **Name** — agree on the BC's name and gain team consensus.
2. **Purpose** — 1–2 sentences in business language. No tech details.
3. **Strategic Classification** —
   - Core / Supporting / Generic (cite `kb/patterns/core-domain-chart.md`).
   - Revenue / Engagement / Compliance.
   - Wardley evolution: Genesis / Custom / Product / Commodity.
   - Life expectancy (cite `kb/voices/verraes-context-life-expectancy.md`).
4. **Domain Roles** — pick archetype(s) from
   `kb/patterns/bounded-context-archetypes.md`.
5. **Inbound Communication** — messages (Command/Query/Event), collaborators,
   relationship type (cite
   `kb/patterns/context-mapping-patterns.md`).
6. **Outbound Communication** — same notation, outbound.
7. **Ubiquitous Language** — **sacred section**. Seeds/updates
   `projects/<slug>/4-glossary.md`. Invoke the `ubiquitous-language` skill.
8. **Business Decisions** — key rules. If vague, run Example Mapping.
9. **Assumptions** — things we are assuming without full knowledge. Make
   explicit.
10. **Verification Metrics** — how we'd know whether this BC was drawn well.
11. **Open Questions** — unresolved; feed ADR drafts.

## Interrogator behaviours

- **Multiple archetypes on one BC → smell.** If a BC is
  Specification *and* Execution *and* Analysis, it's probably three BCs
  (cite `kb/patterns/bounded-context-archetypes.md § How the interrogator
  should use this`).
- **Brain Context → likely anti-pattern.** If many dependencies point in and
  most business rules live here, push back (cite same file + propose
  decomposition).
- **Technical language in Purpose → push back.** Business language only.
- **Everything labelled Core → suspect.** Cite
  `kb/patterns/core-domain-chart.md` — nothing is differentiating if
  everything is.
- **Vague Business Decisions → require Example Mapping.**
- **No Open Questions → suspicious.** A genuinely modelled BC has open
  questions. Zero = under-explored.
- **Conflicting glossary terms vs other BCs → flag as homonyms explicitly**,
  invoke `glossary-curator` agent.

## Output artifact — `projects/<slug>/6-contexts/<bc-slug>.md`

Uses the 11-section canvas per `kb/ceremonies/bounded-context-canvas.md`.
Template:

```markdown
# Bounded Context Canvas — <BC name>

**Date:** <YYYY-MM-DD>
**References:** kb/ceremonies/bounded-context-canvas.md, kb/patterns/bounded-context-archetypes.md, kb/patterns/core-domain-chart.md

## 1. Name
<name>

## 2. Purpose
<1–2 sentences, business language>

## 3. Strategic Classification
- **Importance:** Core / Supporting / Generic
- **Business Model:** Revenue / Engagement / Compliance
- **Wardley Evolution:** Genesis / Custom / Product / Commodity
- **Life Expectancy:** Permanent / Long / Medium / Short / Episodic (per kb/voices/verraes-context-life-expectancy.md)

## 4. Domain Roles (archetypes)
- <archetype 1> (from kb/patterns/bounded-context-archetypes.md)
- <archetype 2 — if second, verify not a smell>

## 5. Inbound Communication
| Collaborator | Message Type | Message | Relationship |
|---|---|---|---|
| <BC/system/user> | Command/Query/Event | <name> | <P/CS/CF> |

## 6. Outbound Communication
(same format)

## 7. Ubiquitous Language
- See `projects/<slug>/4-glossary.md` for the source of truth.
- **Terms owned by this BC:** <list>
- **Terms with cross-BC homonyms:** <list>

## 8. Business Decisions
- <rule> (source: 3b-example-maps/<story>.md)
- ...

## 9. Assumptions
- <explicit assumption>
- Life-expectancy assumption (if relevant)

## 10. Verification Metrics
- <metric that would tell us if this BC is drawn well>

## 11. Open Questions
- <question>
- ...
```

## Post-run

- New terms in § 7 → flow them into `projects/<slug>/4-glossary.md` via the
  `ubiquitous-language` skill.
- Rules in § 8 that lack examples → run `/ddd:example-map`.
- Open questions in § 11 with decision-significance → draft ADRs.

## References (from kb/)

- `kb/ceremonies/bounded-context-canvas.md` — canonical canvas v5.
- `kb/patterns/bounded-context-archetypes.md` — archetype list for § 4.
- `kb/patterns/context-mapping-patterns.md` — relationship types for § 5–6.
- `kb/patterns/core-domain-chart.md` — importance classification for § 3.
- `kb/voices/verraes-context-life-expectancy.md` — life-expectancy slot.
