---
title: Anti-Pattern — Anemic Domain Model
source: native (interrogator-facing; complements kb/canonical/fowler-anemic-domain-model.md)
author: native
license: internal
tier: 3
tags: [anti-pattern, anemic, interrogator]
ingested: 2026-04-19
vendored_from: native
---

# Anti-Pattern — Anemic Domain Model

> **Interrogator rule (per CLAUDE.md §5):** If the user's proposal matches this
> anti-pattern, **name it explicitly** and explain why. Do not quietly accept
> it.

## What it looks like

- The "domain model" consists of data classes with only getters/setters.
- All business logic lives in `*Service` classes that operate on those data
  classes.
- Invariants are enforced by scattered validators rather than the aggregate
  itself.
- Persistence entities are used as the domain model (same ORM class serves
  both purposes).

## Pushback script (interrogator-facing)

When the user describes a design like this, the interrogator says
(substantively, not verbatim):

> "What you're describing is what Fowler calls the **Anemic Domain Model**
> (see `kb/canonical/fowler-anemic-domain-model.md`). The data is in the
> `Order` class, but the *behaviour* — the rules that keep an order
> consistent — lives in `OrderService`. The rich-model advantage of DDD isn't
> delivered by that design. Two questions:
>
> 1. What invariants must an `Order` maintain? Name them.
> 2. Where do those invariants live in your design right now?
>
> If the answers don't point inside the aggregate, we have an anaemic model.
> There are two honest options: (a) move the invariants into the aggregate,
> or (b) call this a transaction-script design (not a domain model), and drop
> the DDD vocabulary."

## Why this pushback is worth insisting on

1. **It's the single most common DDD failure mode in the industry.** Teams
   adopt DDD vocabulary but keep procedural habits, then conclude "DDD
   didn't work for us" when the real issue is they didn't do it.
2. **It collapses the cost/benefit argument.** DDD's cost is higher than CRUD
   + services; its benefit is invariant enforcement and evolving models. If
   you pay the cost without getting the benefit, you've made a bad trade.
3. **Anaemia spreads.** Once the team learns to put logic in services, every
   new aggregate follows suit. Early correction is cheap.

## Valid counter-cases

An anaemic model is genuinely the right design when:

- The BC has **no business invariants** to enforce — e.g. a pure CRUD admin
  screen, a data-entry form.
- The BC is a **read model / projection** — behaviour belongs in the command
  side.
- The BC is a **gateway / funnel** with pass-through semantics.

In these cases: fine. **But call it what it is.** "Transaction script" is the
honest name. Reserving "domain model" for cases with behaviour preserves the
term's meaning.

## How to cite

> Per `kb/anti-patterns/anemic-domain-model.md`, this matches the anaemic
> domain model anti-pattern — data in one place, behaviour in another.
> Either move the invariants into the aggregate, or reframe this as a
> transaction script.

## Related references

- `kb/canonical/fowler-anemic-domain-model.md` — the canonical Fowler article
  summary.
- `kb/canonical/vernon-aggregate-rules.md § Rule 1` — invariants motivate
  aggregates.
- `kb/patterns/aggregate-design-canvas.md § 3 State Transitions` — anaemic
  state transitions are the canvas's smell for this anti-pattern.
