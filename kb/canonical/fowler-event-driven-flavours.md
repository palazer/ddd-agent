---
title: What do you mean by "Event-Driven"? (Fowler bliki)
source: https://martinfowler.com/articles/201701-event-driven.html
author: Martin Fowler
license: fair-use summary
tier: 1
tags: [events, event-driven, fowler, cqrs, event-sourcing]
ingested: 2026-04-19
vendored_from: native
adaptation_notes: Condensed summary of Fowler's 2017 essay distinguishing four distinct things people call "event-driven". Critical because ideation conversations constantly conflate these.
---

# Four Flavours of "Event-Driven" (per Fowler)

Fowler's 2017 essay distinguishes four genuinely different things that people
lump under "event-driven architecture". Getting this distinction right is
**essential** in ideation because the trade-offs differ wildly.

Skills and the interrogator should use these names precisely.

## 1. Event Notification

A system sends a notification when something happens. **Receivers act on the
notification without asking the sender for more info.**

Example: `OrderPlaced` event → Inventory reduces stock; no call back to Order.

- **Benefit:** loose coupling. Sender doesn't know what receivers do.
- **Trade-off:** easy to lose track of the overall flow — "what happens when
  an order is placed?" requires reading all subscribers.

## 2. Event-Carried State Transfer

An event carries **enough state** that receivers can do their work without
calling back to the sender.

Example: `OrderPlaced` event includes full line items, prices, customer
details → Billing can invoice without re-fetching.

- **Benefit:** receivers are resilient to sender downtime; lower coupling at
  read time.
- **Trade-off:** consumers hold copies of data (eventual consistency, data
  duplication); schema changes are harder to coordinate.

## 3. Event Sourcing

The **source of truth** is the event log itself. Current state is derived by
replaying events.

Example: Git. Or accounting ledgers. Or `OrderCreated`, `LineItemAdded`,
`AddressChanged` — replay to reconstruct the order.

- **Benefit:** full audit trail; temporal queries ("what did this look like
  last Tuesday?"); replay to rebuild projections.
- **Trade-off:** implementation complexity (projections, snapshots, event
  schema evolution); every read requires a projection to materialise.
- **Important:** Event Sourcing is orthogonal to Event Notification — you can
  have either without the other.

## 4. CQRS (Command Query Responsibility Segregation)

**Different models for reads and writes.** Writes go to a command model
(often an aggregate); reads go to one or more projections/read models.

- **Benefit:** each model optimised for its use; scales reads and writes
  independently.
- **Trade-off:** two models to maintain; synchronisation latency (usually via
  events).
- **Important:** CQRS does **not** require event sourcing. It can be applied
  with plain databases and a synchronisation job.

## The big idea

These four are **genuinely different** — combining them is possible, but each
choice is independent:

| Flavour | Decides |
|---|---|
| Event Notification | Whether events decouple components at all |
| Event-Carried State Transfer | Whether events carry enough to be read-autonomous |
| Event Sourcing | Whether events are the source of truth |
| CQRS | Whether reads and writes use the same model |

A system might be CQRS without Event Sourcing, Event Notification without
State Transfer, etc.

## Why this matters in ideation

In an Event Storming workshop (`kb/ceremonies/event-storming.md`), the group
will produce a wall of past-tense events. That does **not** commit them to any
of the four flavours — the wall is a discovery artifact.

A decision about which flavour to use is a separate, later conversation —
often after candidate Bounded Contexts are identified. Conflating the two is
a common failure mode.

## Interrogator pushbacks powered by this

- User says "we'll do event-driven" without specifying flavour → **force the
  distinction**: "Do you mean notification, state transfer, event sourcing,
  or CQRS? They're different choices."
- User says "let's event-source the whole system" → **push back**. Event
  Sourcing has real implementation costs; typically only Core domains
  (`kb/patterns/core-domain-chart.md`) merit it. Supporting/Generic should
  not.
- User says "CQRS means event sourcing" → **correct explicitly** with citation.
- User reaches for Event Sourcing to "get audit logs" → challenge. A simple
  audit log may suffice; event sourcing is much more than logging.

## Fowler's own caveat

> "These distinctions are useful, but not always sharply drawn in real
> systems."

The flavours blend in practice. The point isn't to force purity — it's to
prevent the team from believing they've made *one* decision when they've
actually made *four separately*.

## Related references

- Original essay: https://martinfowler.com/articles/201701-event-driven.html
- [Mathias Verraes' take on messaging flavours](kb/voices/verraes-messaging-flavours.md) —
  finer-grained than Fowler's four.
- `kb/ceremonies/event-storming.md` — the discovery ceremony; neutral on
  flavour.
