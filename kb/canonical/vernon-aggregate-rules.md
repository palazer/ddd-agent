---
title: Vernon's Aggregate Design Rules
source: Vaughn Vernon, "Effective Aggregate Design" (3-part series, dddcommunity.org, 2011)
author: Vaughn Vernon
license: fair-use summary (freely redistributed by dddcommunity.org)
tier: 1
tags: [aggregate, vernon, tactical-design, consistency]
ingested: 2026-04-19
vendored_from: native
adaptation_notes: Condensed from Vernon's three PDFs. Even though v1 doesn't do tactical code, these rules inform late-ideation aggregate naming — and they're the canonical "small aggregates" argument.
---

# Vernon's Four Rules of Aggregate Design

Vaughn Vernon's *Effective Aggregate Design* (2011, 3 parts, dddcommunity.org)
is the canonical treatment of **why small aggregates win**. The four rules
below are the operational guidance.

## Rule 1 — Model True Invariants in Consistency Boundaries

An aggregate exists to protect a **business invariant** — a rule that must
hold true at every transaction boundary. If a rule is not a business
invariant, it does not motivate an aggregate.

**Test:** If rule X is violated for 5 seconds while the system eventually
corrects it, does the business care? If not, it's not an invariant — model it
as a corrective policy (see `kb/patterns/aggregate-design-canvas.md` §5).

## Rule 2 — Design Small Aggregates

Prefer aggregates that contain **one root entity plus a small graph**. Large
aggregates are a bad default because:

- **Concurrency conflicts** grow with size (more commands conflict on one
  instance).
- **Memory pressure** grows with size (whole aggregate loaded on each command).
- **Transaction duration** grows with size (locks held longer).
- **Team autonomy** suffers (coupling concentrates).

Vernon's stronger claim: **most aggregates should be one entity**. Additional
entities only when a true invariant demands them.

## Rule 3 — Reference Other Aggregates By Identity

Never hold a direct object reference to another aggregate. Reference by ID
instead. Reasons:

- Forces the boundary to be real (you cannot accidentally mutate across it).
- Enables independent persistence.
- Enables eventual consistency between aggregates.

**Corollary:** any operation spanning two aggregates must be modelled as a
**domain event + handler**, not a direct call across aggregate boundaries
within one transaction.

## Rule 4 — Use Eventual Consistency Outside the Boundary

Inside one aggregate: strong consistency (one transaction, ACID).
Between aggregates: eventual consistency (via events and handlers).

This is a design decision, not a technical one: you are claiming that the
business can tolerate brief inconsistency between those two aggregates. If it
cannot, that's a signal they should be one aggregate (subject to Rule 1).

## The meta-rule

> **Don't design an aggregate to model the data; design it to protect an
> invariant.**

An aggregate whose boundary doesn't protect any invariant is just a cluster
of objects — it shouldn't be an aggregate at all.

## How this file should be cited

When the interrogator is in late-ideation and the user is drawing aggregate
boundaries, cite the four rules by name:

> Per `kb/canonical/vernon-aggregate-rules.md § Rule 2`, aggregates should be
> small — this candidate has 4 child entities, which suggests you may be
> protecting more than one invariant in one boundary.

## Interrogator pushbacks powered by these rules

- User proposes a large aggregate → **Rule 2**: is there a true invariant
  requiring this size? Could some children be separate aggregates linked by ID?
- User proposes cross-aggregate operation in one transaction → **Rules 3 & 4**:
  either merge the aggregates (if invariant demands), or model as domain event.
- User proposes an aggregate with no enforced invariants → **Rule 1**: is this
  actually an aggregate, or a value-object container / read model?
- User insists "the business can't tolerate any delay between these two" →
  **Rule 4**: challenge the business premise; "milliseconds of delay" is usually
  fine; "minutes or hours" may genuinely motivate merge.

## Related references

- Vernon's original 3-part series: https://www.dddcommunity.org/library/vernon_2011/
- Vernon, *Implementing Domain-Driven Design* (Addison-Wesley, 2013) — the
  book-length treatment.
- `kb/patterns/aggregate-design-canvas.md` — McLoughlin/Huizendveld's canvas
  that operationalises these rules.
