---
title: Cynefin Framing — When (not) to apply DDD
source: https://cynefin.io/wiki/Cynefin_Domains + Liz Keogh, "Cynefin for Developers"
author: Dave Snowden (Cynefin), adapted for DDD context by Liz Keogh, Nick Tune
license: fair-use summary (Cynefin is Snowden's IP, freely described)
tier: 1
tags: [cynefin, complexity, framing, when-not-to-ddd]
ingested: 2026-04-19
vendored_from: native
adaptation_notes: The framing gate for /ddd:ideate. This is the *first* step because it prevents us applying expensive DDD machinery to problems that don't need it.
---

# Cynefin — Deciding Whether DDD Fits

Cynefin (Welsh, "habitat") is a sense-making framework by Dave Snowden. It
classifies a problem into one of five domains, each of which calls for a
different *kind* of response. DDD is not the right tool for all of them.

## The five domains

| Domain | Cause/Effect | Best approach | Example |
|---|---|---|---|
| **Clear** (formerly *Simple* / *Obvious*) | Known knowns. Clear cause → effect. | **Sense → Categorise → Respond.** Apply a best practice. | Password reset flow; CRUD admin screens. |
| **Complicated** | Known unknowns. Requires expertise to analyse, but the answer exists. | **Sense → Analyse → Respond.** Apply a good practice, bring in experts. | Database sharding strategy; standard tax calculation. |
| **Complex** | Unknown unknowns. Cause/effect only visible in retrospect. | **Probe → Sense → Respond.** Safe-to-fail experiments; emergent practice. | Multi-sided marketplace; new market entry; regulatory change. |
| **Chaotic** | No discernible cause/effect. Crisis. | **Act → Sense → Respond.** Stabilise first. | Active outage; litigation in progress; company-threatening incident. |
| **Confused** (formerly *Disorder*) | You don't know which domain you're in. | Break the problem into pieces, classify each. | Any time multiple team members disagree about which of the above applies. |

## The DDD fit heuristic

| Domain | DDD fit | What this agent does |
|---|---|---|
| **Clear** | ❌ Overkill. CRUD / forms are fine. | **Warn** — propose simpler approach (transaction script, CRUD screens, off-the-shelf SaaS). Offer to proceed only if the user insists. |
| **Complicated** | ⚠️ Partial. Use strategic DDD for boundaries; skip heavy tactical machinery. | **Partial loop** — run Event Storming + Bounded Context work, but downweight ceremonies around complex business rules (Example Mapping, etc.). |
| **Complex** | ✅ Core use case. DDD's iterative/discovery nature matches. | **Full loop** — all 7 ideation steps. This is what the agent was built for. |
| **Chaotic** | ❌ Dangerous. You cannot model what isn't stable. | **Refuse** — "Stabilise first, model later. What one thing would bring the situation back to Complex or Complicated?" |
| **Confused** | Diagnose first. | **Decompose** — split the problem and classify each sub-problem before choosing ceremonies. |

## How to classify a prompt

Questions to ask the user (in order):

1. **"Has this problem been solved before, in a way you can buy or copy?"**
   → Yes → Clear or Complicated. Push toward buy-don't-build
   (`kb/patterns/core-domain-chart.md` generic domain treatment).

2. **"If we ship the most obvious solution, is the business risk high if it
   turns out to be wrong?"**
   → Yes → at least Complicated. If *the answer might be unknowable without
   trying*, Complex.

3. **"Are domain experts certain about the rules, or do reasonable experts
   disagree?"**
   → Disagree → Complex. Expert disagreement is the canonical Complex signal.

4. **"Is there an active crisis (outage, lawsuit, reputational threat) we are
   trying to model our way out of?"**
   → Yes → Chaotic. Refuse DDD until stabilised.

5. **"If I classified this as X, would you nod or push back?"**
   → Push back consistently → Confused. Decompose before continuing.

## Edges & gotchas

- **Domains can coexist in one product.** A charging-stations platform might
  have a *Complex* pricing/optimisation core and a *Clear* user-profile
  context. Classify at the **sub-problem** level, not the whole product.
- **Domains migrate.** As understanding grows, a Complex sub-problem often
  moves to Complicated (expertise accumulates) or even Clear (best practice
  emerges). Re-classify periodically.
- **"Best practice" is a Clear-domain concept.** Talk of best-practices in a
  Complex domain is a category error — the right phrase is *emergent
  practice*. Push back on "best practices" language for Complex domains.
- **The Cynefin cliff.** There is a hard edge between Clear and Chaotic (not a
  gradient) — a Clear system under-attended slides straight to Chaotic. Worth
  naming but out of v1 scope.

## Interrogator behaviour (Plöd-L3)

- If the user opens with a Clear-domain prompt ("build me a todo app") →
  flag it, propose off-the-shelf, and *refuse to continue the full ideation
  loop* without an explicit override. Citing:
  `kb/patterns/core-domain-chart.md` — generic domains should be bought.
- If the user presents a crisis ("we're being sued over data retention, we
  need to rebuild auth") → refuse the modelling work. Ask: "Is this an
  active fire? Is modelling the right response *right now*, or is
  containment?"
- If the user's answers shift across the questions (sometimes it's novel,
  sometimes known-unknown) → classify as Confused. Decompose before
  proceeding.
- If the user classifies everything as "Complex" → challenge. The most
  honest classifications have a mix.

## Output artifact

Cynefin framing lives at the top of the orchestration — it is not a
per-ceremony artifact but rather feeds `projects/<slug>/1-cynefin-framing.md`:

```
## Cynefin framing

**Overall classification:** Complex (with Complicated sub-problems)
**Evidence:**
- Domain experts disagree on <X>
- No off-the-shelf solution matches <Y>
- Market positioning novel on <Z>
**Complicated sub-problems (may be downweighted):**
- <sub-problem> — solvable with known pattern <pattern>
**Proceed:** yes, with full ideation loop.
```

## Related references

- Dave Snowden, [Cynefin overview](https://cynefin.io/wiki/Cynefin_Domains)
- Liz Keogh, [Cynefin for developers](https://lizkeogh.com/2012/03/01/using-cynefin-to-understand-software-development/)
- Nick Tune, [Complexity is the enemy — core domain first](https://medium.com/nick-tune-tech-strategy-blog/)
- Harvard Business Review, Snowden & Boone, [A Leader's Framework for Decision Making](https://hbr.org/2007/11/a-leaders-framework-for-decision-making) — original popular article.
