---
title: "Context Life Expectancy" — Verraes
source: https://verraes.net/2022/01/context-life-expectancy/ (approx; also DDD Europe talks)
author: Mathias Verraes
license: fair-use summary
tier: 2
tags: [bounded-context, lifecycle, verraes, voices, temporal]
ingested: 2026-04-19
vendored_from: native
---

# Verraes — Context Life Expectancy

Different Bounded Contexts have different **life expectancies**. Some will
exist for decades; others are explicitly temporary. Naming the expected
lifespan of a BC is a rarely-done-but-load-bearing modelling decision.

Verraes' framing: many architectural mistakes come from pretending every BC
is forever.

## Typical life expectancies

| Expectancy | Example | Implications |
|---|---|---|
| **Effectively permanent (decades)** | Core domain accounting BC | Invest deeply in model; robust schema evolution; skilled team. |
| **Long (years)** | Standard supporting BC | Normal care; plan for ~2 major refactors over its life. |
| **Medium (1–3 years)** | A product-feature BC with clear scope | Fit-for-purpose; keep it thin; plan for retirement. |
| **Short (months)** | Migration / Bubble BCs | **Plan for deletion from day one.** Don't over-design. |
| **Episodic** | Campaign/event BCs (one Olympics, one Black-Friday pricing engine) | Explicit start/end; data archival plan. |

## The Bubble Context pattern (`kb/patterns/bounded-context-archetypes.md`)

Bubble Contexts sit in front of legacy systems while they're being replaced.
By design, they are **short-lived**: they exist to enable migration and then
get absorbed or deleted.

The mistake: building a Bubble with the same rigor as a permanent core BC.
It should be lean, well-bounded, and explicitly marked "temporary".

## Why naming life expectancy matters

- **Modelling investment.** A 6-month BC doesn't deserve the same invariant
  rigor as a permanent one. A permanent one deserves more than most get.
- **Team allocation.** Permanent core BCs need stable teams. Short-lived BCs
  can be tackled by flex capacity.
- **Interface design.** Contracts for a permanent BC must be stable and
  versioned with care. Contracts for a short-lived BC can be
  fit-for-current-consumers.
- **Retirement plan.** Every finite-expectancy BC needs a stated exit
  criterion: what condition indicates this BC is ready to be removed?

## Interrogator pushbacks powered by this

- User describes a BC but gives no sense of whether it's permanent or
  temporary → **ask explicitly**: "How long do you expect this BC to exist?
  Decades, years, months?" The answer shapes investment.
- User proposes a Migration/Bubble BC but describes a permanent-scale team
  investment → push back. Plan for retirement.
- User has ~10 candidate BCs, all framed as permanent → suspicious. Real
  systems have a mix.
- User proposes an Episodic BC (campaign-shaped) as part of core domain →
  check; usually Episodic BCs are Supporting or Generic.

## How this file should be cited

> Per `kb/voices/verraes-context-life-expectancy.md`, this looks like a Bubble
> Context — expected to live only while the legacy migration is in
> progress. Keep the model thin and name a retirement criterion.

## Fold into Bounded Context Canvas

When filling `kb/ceremonies/bounded-context-canvas.md`, add life-expectancy
into **§3 Strategic Classification** (as a fourth classification alongside
core/supporting/generic, revenue/engagement/compliance, and Wardley
evolution). Or call it out in **§9 Assumptions** explicitly:

```
## 9. Assumptions
- This BC is a **Bubble Context**, expected to live ~12 months while the
  legacy Billing system is replaced.
- Retirement criterion: all new contracts flow through the new Billing BC
  and historical data has been migrated.
```

## Related references

- Verraes, [verraes.net on context lifecycle](https://verraes.net/)
- Bubble / Autonomous Bubble archetypes:
  `kb/patterns/bounded-context-archetypes.md`
- Temporal Modelling: `kb/voices/verraes-temporal-modelling.md`
