---
title: Bounded Context (Fowler bliki)
source: https://martinfowler.com/bliki/BoundedContext.html
author: Martin Fowler
license: fair-use summary (martinfowler.com content is freely excerpted)
tier: 1
tags: [bounded-context, fowler, strategic-design]
ingested: 2026-04-19
vendored_from: native
adaptation_notes: Condensed summary of Fowler's bliki entry on Bounded Context. This is the most-cited short definition of the concept in the industry.
---

# Bounded Context (per Fowler)

A Bounded Context is a **boundary within which a single model is consistent**.
Fowler's bliki entry (2014) is the canonical short-form introduction.

## Fowler's core points

### The problem Bounded Contexts solve
In any large system, different parts use the same word to mean different
things. "Customer" in a Sales context is a lead; in Support it's an installed
base; in Billing it's an account. Forcing a single unified model across all
of them produces a model that is either (a) an ambiguous compromise nobody
uses correctly, or (b) a huge, complex model that over-serves every
individual use.

> *"Multiple canonical models"* — Fowler's term for the plurality we're
> accepting.

### What a Bounded Context *is*
A named boundary. Inside it:
- **One ubiquitous language.** Each term has one meaning.
- **One model.** One coherent set of concepts and rules.
- **One team's ownership** (ideally — a BC should fit on a team's cognitive
  load budget).

Outside it: **translation**. Always.

### The interplay with teams
Fowler is explicit: BCs are organisational, not just technical. Two teams with
wildly different languages and priorities **should** have separate BCs — the
boundary reflects their actual independence. Forcing shared code across this
boundary creates integration hell.

### Size guidance
Fowler resists a strict size rule but notes:
- A BC that fits **one team** (6–10 people) is typical.
- Many small BCs are better than few huge ones — small BCs stay coherent.
- **BC ≠ microservice.** A BC may be implemented as one microservice, several,
  or part of a larger monolith. See Verraes on this
  (`kb/voices/verraes-bc-not-1-on-1.md`).

### Key implementation consequences
- **Translation layers (ACL).** When two BCs must exchange information, there
  is always a translation. Implicit translation is bugs waiting to happen;
  explicit translation is an Anticorruption Layer (see
  `kb/patterns/context-mapping-patterns.md`).
- **Duplication is often correct.** Two BCs having their own "Customer"
  representation — even with overlapping fields — is a feature, not a bug. It
  reflects genuinely different concepts.

## Useful quotes from the bliki

> "An explicit boundary allows team members to know what must be consistent
> and what can develop independently."

> "Large applications have multiple canonical models. Attempting to describe
> the whole using a single canonical model leads to confusion."

## How this file should be cited

When a skill or agent needs a one-paragraph "what is a Bounded Context" for
the user:

> Per `kb/canonical/fowler-bounded-context.md`, a Bounded Context is a boundary
> within which a single model and ubiquitous language is consistent. Across
> the boundary, terms must be explicitly translated.

## Interrogator pushbacks powered by this

- User says "let's have one User model for the whole system" → cite Fowler:
  multiple canonical models. Ask what "User" means in each BC.
- User maps BCs 1:1 to microservices → cite `kb/voices/verraes-bc-not-1-on-1.md`.
- User wants to share a library/model type across BCs → is this a Shared
  Kernel (`kb/patterns/context-mapping-patterns.md`)? Push back — SK is
  expensive; OHS/PL is usually better.

## Related references

- Original bliki: https://martinfowler.com/bliki/BoundedContext.html
- Evans' canonical definition: `kb/canonical/evans-ddd-reference.md § Bounded
  Context`
- Verraes on BC-vs-microservice non-equivalence:
  `kb/voices/verraes-bc-not-1-on-1.md`
