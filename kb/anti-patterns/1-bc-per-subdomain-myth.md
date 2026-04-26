---
title: Anti-Pattern — "1 BC per Subdomain" (and "1 BC per Microservice")
source: native (draws on kb/voices/verraes-bc-not-1-on-1.md)
author: native
license: internal
tier: 3
tags: [anti-pattern, bounded-context, subdomain, microservice, interrogator]
ingested: 2026-04-19
vendored_from: native
---

# Anti-Pattern — "1 Bounded Context Per Subdomain" (or Per Microservice)

> **Interrogator rule (per CLAUDE.md §5):** If the user draws BCs that mirror
> their subdomains or microservices 1:1, **name the anti-pattern explicitly**
> and push back.

## What it looks like

- The candidate BC list has exactly the same count and names as the
  business's subdomains (or the planned microservices).
- Each BC has one team, one database, and one deployment unit — with no
  question of whether the *model* actually splits that way.
- The team believes "we decided our subdomains, so we have our BCs".

This is the single most common strategic-design error.

## Why it's wrong

Verraes' distinction (`kb/voices/verraes-bc-not-1-on-1.md`) is load-bearing:

| Concept | Axis | Decided by |
|---|---|---|
| **Subdomain** | Problem space | Business analysis |
| **Bounded Context** | Solution space | Model design |
| **Microservice** | Physical space | Deployment needs |

Collapsing these is not one decision made well — it's three decisions made
badly by avoiding any of them.

## Pushback script

> "The list of BCs you've drawn is identical to your subdomain list (or your
> microservice plan). That's a red flag. Per
> `kb/voices/verraes-bc-not-1-on-1.md`, BCs, subdomains, and microservices
> are three separate concerns:
>
> - A subdomain can split into multiple BCs if the *model* benefits (classic
>   example: a Pricing subdomain with a Pricing-Rules BC and a Pricing-Engine
>   BC).
> - A BC can span multiple subdomains if they share a consistent model —
>   common during migrations or at integration seams.
> - A BC is implemented as microservices based on *deployment* needs, not
>   modelling ones.
>
> Let's revisit each candidate: are you drawing this BC because the *model*
> coheres here, or because the *team* or *deploy unit* does? If the latter,
> we may be missing a real boundary."

## How to probe whether the 1:1 mapping is *actually* right

Sometimes the 1:1 is correct — the model, team, and deployment genuinely
align. To check:

- **For each BC**, can you name a term that means something different
  *inside* vs. *outside* the boundary? (If not → the model has no real
  boundary, the BC may be an illusion.)
- **For each BC**, is there a workflow/story that lives entirely inside? (If
  all the value-producing workflows cross every boundary, your BCs may be
  technical, not domain-model boundaries.)
- **Across pairs of BCs**, is there an actual translation needed, or just a
  field rename? (Rename-only = not a BC boundary; it's a data layout.)

## Valid counter-cases

- **Very small systems.** A 2-person project with 3 clear domain concepts
  might genuinely have 3 subdomains = 3 BCs = 3 services. Fine. But verify
  the mapping is a real finding, not a default.
- **Migration-imposed mapping.** During a migration, organisational
  constraints may temporarily collapse the mapping. That's fine if it's
  explicitly named as a transitional state
  (`kb/voices/verraes-context-life-expectancy.md`).

## Related anti-patterns this often comes with

- **Database per Microservice → Aggregate per Table.** If 1 microservice = 1
  BC = 1 database, teams often end up with `Aggregate ≈ Table`, violating
  `kb/canonical/vernon-aggregate-rules.md § Rule 2` (small aggregates) by
  giving each DB table an aggregate whether it deserves one or not.
- **"Modular Monolith means no BCs"** — the inverse mistake. Physical
  deployment being one unit doesn't mean conceptual model is one unit.

## How to cite

> Per `kb/anti-patterns/1-bc-per-subdomain-myth.md`, the candidate BC list
> mirrors the subdomain (or microservice) list 1:1 — a textbook smell.
> Let's verify each boundary is a real *model* boundary, not an
> organisational or deployment one.

## Related references

- `kb/voices/verraes-bc-not-1-on-1.md` — the canonical voice on this
  distinction.
- `kb/canonical/fowler-bounded-context.md` — Fowler's BC definition (a
  model boundary).
- `kb/voices/khononov-balancing-coupling.md` — the rigorous coupling
  argument for when to merge / split.
