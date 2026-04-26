---
title: Anti-Pattern — Database-First (Schema-First) Modelling
source: native
author: native
license: internal
tier: 3
tags: [anti-pattern, persistence, schema, orm, interrogator]
ingested: 2026-04-19
vendored_from: native
---

# Anti-Pattern — Database-First (Schema-First) Modelling

> **Interrogator rule (per CLAUDE.md §5):** If the user defines the domain
> model by designing a database schema first, **name the anti-pattern** and
> redirect.

## What it looks like

- "I've drafted the tables; let's see what aggregates we need."
- The ER diagram is the primary artifact; the event storm / stories are
  absent.
- The "domain model" is the ORM entity classes, with no distinction between
  persistence shape and domain shape.
- Aggregates are named after tables; relationships are named after foreign
  keys.

## Why it's wrong

A database schema is a **storage shape**, optimised for efficient query and
storage. A domain model is a **behaviour shape**, optimised for enforcing
invariants and expressing the ubiquitous language. They are routinely
different, and conflating them causes:

- **Aggregates shaped by joins, not invariants.** An `Order` aggregate that
  contains `LineItems`, `CustomerSnapshot`, `ShippingAddress` because they're
  all in tables the `Order` FKs to — even if no single invariant spans them.
- **Anaemic models** (`kb/anti-patterns/anemic-domain-model.md`). If the
  "domain model" is just the ORM entities, behaviour has nowhere obvious to
  live and drifts into services.
- **Foreign keys reified as domain concepts.** A `customer_id` on `orders`
  is a storage detail — it doesn't mean the Customer aggregate lives inside
  the Order aggregate. Schema-first thinking can't tell the difference.
- **Cross-BC leakage.** A single schema spanning multiple BCs destroys the
  boundaries. You end up with one "User" table and seven services that all
  depend on its columns.

## Pushback script

> "The tables you've drafted are a good head start for *storage*, but we
> should be careful: storage shape and domain shape are different. Per
> `kb/anti-patterns/database-first-modelling.md` and
> `kb/canonical/vernon-aggregate-rules.md`, aggregates are built around
> **invariants**, not join graphs.
>
> Can we park the tables for a moment and do Event Storming?
> `kb/ceremonies/event-storming.md`. Once the events are on the wall, we
> can revisit the tables and see whether they still match — and, importantly,
> whether any of the tables should live in separate Bounded Contexts."

## Why this feels right but isn't

Schema-first *feels* productive because:

- Database modelling has a long history and well-defined tools.
- ERDs are legible.
- There's a deliverable at the end.

But the legibility is deceptive — it's a clear artifact about the *wrong
question*. The right question is "what model does this BC need?", not "what
tables does this system need?".

## Valid counter-cases

- **Integrating to an existing database you don't control.** The legacy
  schema *is* a fact of the world. Model it honestly, put an ACL between it
  and any new domain logic (`kb/patterns/context-mapping-patterns.md § ACL`).
- **Reporting / analytics BCs.** The read model *is* the deliverable; schema-
  first here is correct. But name the BC as a reporting BC, not a domain one.
- **Pure data-migration work.** Moving data from A to B. The schemas are the
  primary artifact. Not a modelling exercise.

In all valid counter-cases: **name the context explicitly**. "This is a
schema-first piece of work because X" — don't pretend it's domain modelling.

## Related sibling anti-patterns

- **"ORM-driven design."** Same disease, slightly different symptom: the
  framework (Hibernate / ActiveRecord / Entity Framework) shapes the model,
  not the domain. The fix is the same: model the domain first; persistence
  is a serialisation concern.
- **"CRUD as architecture."** If every operation is Create/Read/Update/Delete
  on tables, you probably don't have a domain model — you have a data entry
  application. That's fine (call it what it is); it's not DDD.

## How to cite

> Per `kb/anti-patterns/database-first-modelling.md`, starting from the
> schema tends to produce aggregates shaped by joins instead of invariants.
> Let's run Event Storming first and revisit the schema once the model has
> emerged.

## Related references

- `kb/anti-patterns/premature-tactical-design.md` — the closely-related
  entity-first sibling.
- `kb/canonical/vernon-aggregate-rules.md § Rule 1` — invariants, not tables.
- `kb/canonical/fowler-anemic-domain-model.md` — the usual downstream
  consequence of schema-first design.
- Eric Evans, *Domain-Driven Design* Ch. 5 ("A Model Expressed in Software")
  — the foundational argument against persistence-shape-as-model.
