---
title: Temporal Modelling — Verraes
source: https://verraes.net/2019/07/temporal-modelling/ (and DDD Europe talks)
author: Mathias Verraes
license: fair-use summary
tier: 2
tags: [temporal, time, events, verraes, voices]
ingested: 2026-04-19
vendored_from: native
---

# Verraes — Temporal Modelling

One of Verraes' most important themes: **time is usually an invisible actor
in the domain, and most models pretend it isn't**. Making time explicit —
as events, schedules, versions, effective dates, and lifecycles — produces
better boundaries and far fewer bugs.

## The observation

Most "current state" models implicitly assume *now*. Business rules often
depend on *when* something was true, not just *whether*. Examples:

- A tax rate that changes on a specific date — which rate applies to an
  order placed minutes before the change?
- A contract with an effective-from and expires-on.
- A price list valid for a period.
- An address change that should apply to past invoices retroactively — or
  not.

A model that only stores "current state" loses this information and has to
reconstruct it from audit tables or changelogs — which is where
almost-correct bugs live.

## Core techniques Verraes advocates

### 1. **Event Sourcing for temporally-sensitive aggregates**
Not for everything — but for aggregates where *history matters to the
business*, events as source of truth is a clean fit. See
`kb/canonical/fowler-event-driven-flavours.md § Event Sourcing`.

### 2. **Effective/Applicable date pairs**
Instead of one "date" field, model two:
- **Effective from / to** — the period during which the fact is true in the
  *domain*.
- **Recorded at** — when the system was told. Can differ (backdated entries).

Example: a price valid from Jan 1 — recorded on Dec 15 of the previous year.

### 3. **Lifecycle scoping**
Some aggregates are inherently *temporal windows*: `BillingPeriod`,
`Tournament`, `AuctionRound`. Modelling them as explicit lifecycle objects
(not "running aggregates" that live forever) matches the business reality.
See `kb/voices/verraes-context-life-expectancy.md`.

### 4. **Scheduled/Reminder messages as first-class citizens**
Time-based triggers ("in 7 days, auto-close the ticket") are often modelled
as side effects of a scheduler. Verraes' pushback: the scheduled action is a
**domain fact** — model it explicitly as a reminder that the domain issued,
with a first-class lifecycle (scheduled → fired, or scheduled → cancelled).

### 5. **Versioned rules**
Business rules often evolve. Rather than mutating them in place (with
migration pain), version them: `RefundPolicyV2` active from a date.
Retrospective changes apply the policy that was current *when the fact
occurred*.

## Interrogator pushbacks powered by this

- User proposes storing "current price" only → ask: does a past order need
  to reconstruct the price at the time of ordering? If yes, the model needs
  a price history, not just a current price.
- User says "we'll just add an `updated_at` field" → this captures *when the
  row changed in the DB*, not *when the fact became true in the domain*. They
  are different. Push on effective/recorded distinction.
- User has a long-lived aggregate with no natural lifecycle → propose scoping
  to a temporal window (citing Aggregate Design Canvas § 9 Size +
  `kb/voices/verraes-context-life-expectancy.md`).
- User implements reminders as cron jobs running queries → push toward
  explicit scheduled-message aggregates.
- User decides rules must change globally, affecting past data → stop. Ask:
  should past facts be replayed under the new rule, or preserved as they were
  under the old rule? Usually the answer is "preserved".

## A heuristic test: "When was this true?"

For every piece of information in the proposed model, ask:

> **"If this changes in six months, will we need to know what the value used
> to be?"**

If yes → it's temporal. Model it that way. If no → a scalar field is fine.

## How this file should be cited

> Per `kb/voices/verraes-temporal-modelling.md`, the proposed model treats
> `price` as a single current value — but the business needs the price at the
> time of each past order. Introduce a temporal model (price history, or
> event-sourced aggregate).

## Related references

- Verraes, [Temporal Modelling](https://verraes.net/2019/07/temporal-modelling/)
- Verraes, [Context Life Expectancy](kb/voices/verraes-context-life-expectancy.md)
- Fowler, [Bi-Temporal History](https://martinfowler.com/articles/bitemporal-history.html)
- Event Sourcing flavour: `kb/canonical/fowler-event-driven-flavours.md`
