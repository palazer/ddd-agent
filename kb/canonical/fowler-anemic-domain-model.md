---
title: Anemic Domain Model (Fowler bliki)
source: https://martinfowler.com/bliki/AnemicDomainModel.html
author: Martin Fowler
license: fair-use summary
tier: 1
tags: [anti-pattern, anemic, fowler, tactical]
ingested: 2026-04-19
vendored_from: native
adaptation_notes: Condensed summary of Fowler's bliki. The classic "anti-pattern" callout. See kb/anti-patterns/anemic-domain-model.md for the interrogator-facing version of this.
---

# Anemic Domain Model (per Fowler)

An **Anemic Domain Model** is a "domain model" in name only — the classes
contain data (getters/setters) but **no behaviour**. All the business logic
lives in service classes that operate on those anaemic data holders.

Fowler's 2003 bliki post (still the canonical reference) calls this an
**anti-pattern** — a seemingly-good idea that systematically goes wrong.

## Why it *looks* right
- The classes look like domain objects — they have the right names.
- The services have all the behaviour clearly located — "separation of
  concerns".
- It matches ORM-friendly patterns like "persistence entities + service
  layer".

## Why it's actually wrong
- **It violates the fundamental premise of OO** — that data and the behaviour
  operating on it live together.
- **The real model lives in the services**, which are usually procedural.
  Meaning you paid the cost of an OO model without getting its benefit.
- **Rules fragment.** Business invariants are spread across many service
  methods with no single place that says "an Order must have at least one
  line item".
- **Testing is harder**. Service tests require stubbing out persistence to
  test business rules; aggregate tests isolate behaviour naturally.
- **Refactoring is harder**. Moving responsibility requires reshaping many
  services simultaneously.

## Fowler's diagnostic

> "The fundamental horror of this anti-pattern is that it's so contrary to the
> basic idea of object-oriented design; which is to combine data and process
> together."

If you read your "domain model" and every class is a bag of fields with no
methods beyond getters/setters — **that is an anaemic model**, regardless of
what the package is named.

## Counter-arguments Fowler addresses

- **"We're doing CQRS, so we have read models"** — CQRS doesn't require an
  anaemic *write* model. Read projections are separate.
- **"We use functional programming, behaviour lives in pure functions"** —
  that's fine; the point is the *invariants* live with the data they
  constrain, whether in methods or module-level functions colocated with
  the data type. An anaemic model has behaviour dispersed across services
  unrelated to the type.
- **"We need to share data across services"** — DTOs at boundaries are fine;
  the internal model should still be rich.

## How this file should be cited

When the interrogator sees a design where all logic lives in services and the
"domain model" is a pile of getters/setters:

> Per `kb/canonical/fowler-anemic-domain-model.md` (and
> `kb/anti-patterns/anemic-domain-model.md`), this is Fowler's anaemic domain
> model anti-pattern — the classes have domain names but the behaviour lives
> in services. Propose: move invariants into the aggregate.

## Interrogator pushbacks powered by this

- User describes a context where the `Order` class has only getters/setters
  and an `OrderService` has all the logic → **push back**. Either:
  (a) the Order aggregate has no invariants (Vernon Rule 1 — maybe it's not
  an aggregate), or (b) the invariants belong inside it.
- User proposes persistence entities mapping 1:1 to database tables as the
  "domain model" → **push back**. Persistence shape ≠ domain shape.
- "We use Lombok / records / data classes" → fine *as a representation*, but
  check that invariants live somewhere the class can enforce them. Data
  classes + static factory methods + methods on the data class is fine;
  data classes + external service doing all the validation is not.

## When an anaemic model is actually okay

Fowler notes (and Vernon agrees): **if the context truly has no invariants**,
a data-holder + procedural logic is fine. This is what Fowler calls a
"transaction script" architecture.

Critically: **don't call it a domain model then.** It's a transaction-script
design. Naming it honestly prevents the team from pretending it has
object-oriented or DDD properties that it doesn't.

## Related references

- Original bliki: https://martinfowler.com/bliki/AnemicDomainModel.html
- Fowler, *Patterns of Enterprise Application Architecture* — Chapter 9 covers
  the Transaction Script vs. Domain Model trade-off in depth.
- Interrogator-facing version: `kb/anti-patterns/anemic-domain-model.md`.
