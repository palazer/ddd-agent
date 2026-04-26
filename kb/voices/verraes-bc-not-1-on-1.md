---
title: "Bounded Contexts are NOT 1:1 with Subdomains (or Microservices)" — Verraes
source: https://verraes.net/ (multiple posts, especially "Bounded Contexts and Subdomains")
author: Mathias Verraes
license: fair-use summary
tier: 2
tags: [bounded-context, subdomain, microservice, verraes, voices]
ingested: 2026-04-19
vendored_from: native
---

# Verraes — BC ≠ Subdomain ≠ Microservice

Mathias Verraes (prolific DDD practitioner, co-founder of DDD Europe)
repeatedly pushes back on the common but wrong assumption that **Bounded
Contexts, Subdomains, and Microservices are all the same thing**.

This is one of the most-important-and-most-ignored clarifications in DDD.

## The three concepts

| Concept | Defined by | Axis |
|---|---|---|
| **Subdomain** | The *business* | Problem space — part of the business to be modelled. |
| **Bounded Context** | The *model* | Solution space — a model that is consistent within a boundary. |
| **Microservice** | The *deployment* | Physical space — a unit of independent deployment. |

### The three mappings are all many-to-many in principle:

- One subdomain can have multiple BCs (e.g., a Pricing subdomain with a
  Pricing-Rules BC and a Pricing-Engine BC).
- One BC can span multiple subdomains (e.g., a Legacy BC that covers
  authentication and authorisation during a transition).
- One BC can be one microservice, several, or part of a monolith.

## The myth Verraes dismantles

> "We have a Sales subdomain, so we'll have a Sales Bounded Context, which
> will be the Sales Microservice."

Verraes' counter:
- **Subdomain is a business abstraction.** It is whatever makes the business
  sense — often coarse.
- **Bounded Context is a model-design decision.** You choose where to draw
  the line based on *modelling needs* (consistency, language, team cognitive
  load), not business org.
- **Microservice is a deployment decision.** You choose it based on
  *operational needs* (scaling, team boundaries, release cadence).

Collapsing all three into one decision means you've made one badly-reasoned
decision instead of three well-reasoned ones.

## When a subdomain should split into multiple BCs

- The subdomain is large enough that one team can't own all of it.
- The subdomain contains two distinct concepts of the *same word* (e.g., a
  subdomain where "Customer" means two different things depending on the
  workflow).
- Different parts of the subdomain have wildly different change rates.
- Different parts have different integration surfaces.

Verraes' canonical example: **Pricing**. A single subdomain — but
differentiating between "pricing rules" (stable, business-owned) and "pricing
calculation" (fast, high-throughput) is a clean BC split.

## When multiple subdomains should share one BC

- A Legacy BC during a migration.
- A narrow integration layer BC (e.g., a Gateway or Funnel context —
  `kb/patterns/bounded-context-archetypes.md`).

## When BC and microservice should *not* map 1:1

- A BC whose load profile is two very different workloads (a CRUD part + a
  batch part) → split deployment, keep one BC.
- Two related BCs that change together and share a team → deploy as one unit
  (modular monolith), keep two BCs.

## How this file should be cited

When the interrogator catches a 1:1 mapping assumption:

> Per `kb/voices/verraes-bc-not-1-on-1.md`, Bounded Contexts, Subdomains, and
> Microservices are three separate decisions. Mapping them 1:1:1 is the most
> common DDD mistake. Let's decide each deliberately.

## Interrogator pushbacks powered by this

- User's candidate BCs match their org chart 1:1 → **challenge**: are these
  truly separate models, or just the current team structure?
- User says "one BC, one microservice, non-negotiable" → **push back** with
  Pricing example.
- User conflates subdomain and BC in conversation → **insist on the
  distinction** every time. It's load-bearing.
- User has a subdomain with a clear internal split (stable rules vs. fast
  execution) but draws only one BC → propose the split.

## Related references

- Verraes, [verraes.net](https://verraes.net/) — full body of work.
- Nick Tune, [Domain-Driven Distillation](https://medium.com/nick-tune-tech-strategy-blog/) —
  similar clarifications.
- Sam Newman, *Building Microservices* — explicit that microservice ≠ BC.
- Evans' original: `kb/canonical/evans-ddd-reference.md § Bounded Context,
  Subdomain, Core Domain`.
