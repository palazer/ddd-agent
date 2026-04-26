---
title: Aggregate Design Canvas (v1.1)
source: https://github.com/ddd-crew/aggregate-design-canvas
author: ddd-crew (Mathew McLoughlin, Marijn Huizendveld)
license: CC BY 4.0
tier: 1
tags: [aggregate, tactical-design, consistency-boundary, invariant]
ingested: 2026-04-19
vendored_from: ddd-crew/aggregate-design-canvas
adaptation_notes: Vendored verbatim from upstream README. Note — v1 of our agent is ideation-only, but this canvas is still useful as a *thinking* tool during late-ideation when aggregate candidates start to emerge.
---

# The Aggregate Design Canvas

The Aggregate Design Canvas is a modelling tool meant to be used as part of
design-level domain modelling activities.

An **aggregate** is a lifecycle pattern originally described by Eric Evans. By
aggregate, we understand a graph of objects that is a **consistency boundary**
for our domain policies. Depending on the design of the aggregate, we can either
enforce those policies (make them invariant) or be forced to have corrective
policies in place. It is therefore important to design the boundaries of
aggregates well, as they impact behaviours modelled within our domain.

The canvas has a suggested order of working through it.

## How to Use

### 1. Name
Give your aggregate a good name. In some domains it makes sense to include as
part of the name the length of a cycle, or some other indication of the life
span of the aggregate (e.g. `BillingPeriod`).

### 2. Description
Summarise the main responsibilities and purpose of the aggregate. It's a good
idea to include the reasons why such boundaries were chosen and trade-offs made
compared to other designs.

### 3. State Transitions

Usually the aggregate goes through explicit state transitions that impact the
way it can be interacted with.

- **Too many transitions** → process boundaries weren't modelled properly and
  can be split.
- **Very naive / simple transitions** → the aggregate is anaemic and logic was
  pushed out to services.

In this section, list the possible states or draw a small transition diagram.

### 4. Enforced Invariants  &  5. Corrective Policies

One of the main jobs of the aggregate is to **enforce business invariants** —
invariants that protect business logic. Listing the main ones makes sure you
agree on the responsibilities that the aggregate has. A large number of enforced
invariants can indicate high local complexity.

If you decide to change the boundaries and **relax** some invariants (e.g. to
reduce concurrency conflicts), some extra business logic might be required to
correct inconsistencies. We call this **corrective policies**. A large number of
such policies might indicate the business logic was pushed outside the
aggregate — increasing complexity.

Listing both invariants and corrective policies makes design trade-offs
explicit.

### 6. Handled Commands  &  7. Created Events

List all commands that the aggregate is capable of handling and all events
created as a result. Create connectors between them to validate you aren't
missing any building blocks.

### 8. Throughput

Estimate how likely a single aggregate instance is to be involved in
concurrency conflicts. For each metric, estimate the average and maximum.

- **Command handling rate** — how many commands the aggregate processes per unit time
- **Total number of clients** — how many callers issue those commands

Example: a single website basket has one client; a conference-booking aggregate
likely has tens or hundreds.

Plotting Avg and Max for multiple alternatives lets you explicitly discuss
throughput trade-offs. Smaller conflict chance → better UX but higher
implementation complexity. Bigger aggregates → higher conflict chance but fewer
corrective policies.

### 9. Size

Estimate the hypothetical size of the aggregate in **number of events per
aggregate instance**.

- **Event growth rate** — how many events are appended to one instance
- **Lifetime of an instance** — how long the instance lives, and consequently
  how many events accumulate when processing a new command

`OrderCreated` with multiple `LineItems` vs. separate `LineItemAdded` events
will affect overall size. Medium/large numbers of events may slow command
handling — usually solvable with snapshots. Long-lived (potentially infinite)
instances cause archival issues; scope aggregates to a specific time period
(e.g. billing period).

## Tie-ins for the interrogator

- Anaemic state transitions → flag anti-pattern `kb/anti-patterns/anemic-domain-model.md`.
- Large number of invariants + high throughput → aggregate likely too big; push
  back and propose decomposition.
- Long/infinite lifetime → push on scoping to a lifecycle window.

## Related references

- [Domain-Driven Design: A mathematical model for process-oriented Aggregates](https://medium.com/@tPl0ch/domain-driven-design-a-mathematical-model-for-process-oriented-aggregates-pt-i-6aecc8957865)
- [Modelling aggregates with the Aggregate Design Canvas](https://domaincentric.net/blog/modelling-aggregates-with-aggregate-design-canvas)
- [Modelling Aggregates: Invariants vs Corrective Policies](https://domaincentric.net/blog/modelling-business-rules-invariants-vs-corrective-policies)
- [Domain Events vs. Event Sourcing](https://www.innoq.com/en/blog/domain-events-versus-event-sourcing)
