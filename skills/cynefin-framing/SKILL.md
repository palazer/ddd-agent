---
name: cynefin-framing
description: Classify the problem space (Clear / Complicated / Complex / Chaotic / Confused) and decide whether full DDD ceremony applies. The FIRST step of /ddd:ideate — refuses to proceed when DDD is a wrong fit.
argument-hint: <problem-description> [--slug <project-slug>]
allowed-tools: Read, Write, Edit, Glob, Grep, Skill
---

# Cynefin Framing (step 1 of /ddd:ideate)

Before running any ceremonies, classify the problem. This skill is a
**gate** — it can refuse to proceed.

**Canonical reference:** `kb/patterns/cynefin-when-to-ddd.md`.

## Process

Walk the user through the five questions from
`kb/patterns/cynefin-when-to-ddd.md § How to classify a prompt`:

1. Has this problem been solved before, in a way you can buy/copy?
2. If we ship the most obvious solution, is the business risk high if wrong?
3. Are domain experts certain about the rules, or do reasonable experts
   disagree?
4. Is there an active crisis (outage, lawsuit, reputational threat)?
5. If I classified this as X, would you nod or push back?

Based on answers, classify as **Clear / Complicated / Complex / Chaotic /
Confused**.

## Gate behaviour (NON-NEGOTIABLE per CLAUDE.md §1)

| Classification | Action |
|---|---|
| **Clear** | **Decline full ceremony.** Warn overkill. Suggest CRUD / SaaS / transaction script. Proceed *only* if the user explicitly overrides. |
| **Complicated** | **Partial ceremony.** Run Event Storming + BC work; skip heavy Example Mapping for routine rules. Note downweighting in output. |
| **Complex** | **Full ceremony.** This is the canonical use case. Proceed. |
| **Chaotic** | **Refuse.** "Stabilise first, model later." Ask what would bring the situation back to Complex/Complicated. |
| **Confused** | **Decompose.** Split into sub-problems; classify each. Proceed per sub-problem. |

## Interrogator behaviours

- If the user classifies everything as Complex → challenge. Real products
  mix classifications. Cite
  `kb/patterns/cynefin-when-to-ddd.md § Edges & gotchas`.
- If the user opens with "build me a todo app" → Clear. Decline.
- If the user is mid-crisis → Chaotic. Refuse.
- If the user insists Complex when answers are Known/Known → push back but
  proceed if overridden. Mark the classification as disputed.

## Output artifact — `projects/<slug>/1-cynefin-framing.md`

```markdown
# Cynefin Framing — <project name>

**Date:** <YYYY-MM-DD>
**Reference:** kb/patterns/cynefin-when-to-ddd.md

## Overall classification
**<Clear | Complicated | Complex | Chaotic | Confused>**

## Evidence
- <bullet from user's answers>
- ...

## Sub-problems (if classification is Confused, or mixed)
| Sub-problem | Classification | Why |
|---|---|---|
| ... | ... | ... |

## Decision: proceed?
- [x] Yes, with full ceremony / partial / per-sub-problem
- [ ] No — domain not a fit for DDD (Clear / Chaotic)

## Downweighted ceremonies (if Complicated)
- <e.g. skip Example Mapping for routine tax rules>

## User override?
- Default recommendation: <X>
- User elected: <Y> because <reason>
```

## Post-run

- If the gate opens → invoke next skill (event-storming) or return to
  orchestrator.
- If the gate closes → explain politely, propose alternatives, end.

## References (from kb/)

- `kb/patterns/cynefin-when-to-ddd.md` — canonical.
- `kb/patterns/core-domain-chart.md § generic domains` — "buy, don't build"
  argument for Clear/generic cases.
