---
title: Anti-Pattern — Premature Tactical Design
source: native
author: native
license: internal
tier: 3
tags: [anti-pattern, tactical, aggregate, entity, interrogator]
ingested: 2026-04-19
vendored_from: native
---

# Anti-Pattern — Premature Tactical Design

> **Interrogator rule (per CLAUDE.md §5):** If the user jumps to aggregates,
> entities, or code structure before the **strategic** picture exists,
> refuse to proceed and redirect.
> **Also per CLAUDE.md §3:** v1 of this agent *does not* produce tactical
> code — any request that requires it must be declined with redirection to
> Phase 4 (not shipped).

## What it looks like

- The user jumps to "let's define the `Order` entity" before we've mapped
  events, actors, or candidate boundaries.
- The user proposes a schema (`users`, `orders`, `line_items`) before any
  Event Storming.
- The user wants to name aggregates on the wall during Phase 1 of Event
  Storming.
- The user wants the agent to generate code.

## Why it's wrong

DDD's tactical patterns (entities, aggregates, value objects, services) are
**downstream** of strategic decisions (bounded contexts, ubiquitous language,
context maps). Designing tactical code before strategic clarity tends to
produce:

- **Aggregates aligned to tables** rather than invariants
  (`kb/canonical/vernon-aggregate-rules.md § Rule 1`).
- **Cross-BC entities** that leak concepts across boundaries
  (`kb/canonical/fowler-bounded-context.md`).
- **Anaemic models** because the behaviour wasn't yet understood
  (`kb/anti-patterns/anemic-domain-model.md`).
- **Names that don't match the ubiquitous language** because the language
  hasn't been surfaced yet
  (`kb/canonical/fowler-ubiquitous-language.md`).

Brandolini's version (from *Introducing EventStorming*): **aggregates
emerge from events, not the other way around**.

## Pushback scripts

### On entity-first thinking
> "You've named an `Order` entity. Before we commit to that shape, let's step
> back:
>
> - What *events* does an Order go through? (Past tense — what happened?)
> - What invariants must hold at each transition?
> - Which team owns those rules?
>
> Once we have that picture, the aggregate structure falls out cleanly —
> often it's smaller than the entity count you'd draft top-down."

### On schema-first thinking
> "The `users` / `orders` / `line_items` schema you're proposing is a
> persistence shape. It may or may not be the right model shape. Per
> `kb/canonical/fowler-bounded-context.md`, the model lives in one bounded
> context, and persistence is a detail. Let's first get clear on what BCs
> these concepts live in — then we'll see whether the schema fits."

### On code-generation requests
> "v1 of this agent is ideation-only (see `CLAUDE.md` hard rule #3). I can
> take you through Event Storming → Candidate BCs → Context Map → draft
> ADRs, but generating entities/aggregates/framework code is out of scope.
> If you want tactical work now, that's a separate step you'd do with your
> code-capable tooling *after* we've established the strategic picture."

## Valid counter-cases

- **The domain is genuinely Clear/Simple** (per
  `kb/patterns/cynefin-when-to-ddd.md`). In that case, we shouldn't be doing
  DDD at all — a CRUD design is fine, call it a transaction script, and
  move on. But this is a pre-condition to decline DDD, not a licence to do
  tactical-first DDD.
- **Existing implementation under review.** If we're reverse-engineering an
  existing system, starting from code is legitimate — but name it as
  *archaeology*, not *design*, and still run strategic exercises in
  parallel.

## The tell-tale signs the pushback landed

A good pushback surfaces that the user's tactical instinct was *filling in
for absent strategic clarity*. Once strategic work is done, the user often
says "oh, the aggregate is smaller/bigger/different from what I'd have
drafted". That's the ceremony paying off.

If the user still insists on tactical-first after the pushback, note it
(`kb/ceremonies/bounded-context-canvas.md § 9 Assumptions`) and proceed —
but with the disclaimer that the tactical decisions will likely need
revisiting.

## How to cite

> Per `kb/anti-patterns/premature-tactical-design.md`, what you're proposing
> is tactical design before the strategic picture is clear. Let me walk you
> through Event Storming first — the aggregate structure will be cleaner
> once events are on the wall.

## Related references

- `kb/canonical/vernon-aggregate-rules.md § Rule 1` — invariants motivate
  aggregates, not the other way around.
- `kb/patterns/aggregate-design-canvas.md` — the canvas is a *thinking* tool,
  not an entry point.
- `kb/anti-patterns/database-first-modelling.md` — the closely-related
  schema-first sibling.
