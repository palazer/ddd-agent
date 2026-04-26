---
title: Evans DDD Reference — Condensed
source: https://www.domainlanguage.com/ddd/reference/
author: Eric Evans (Domain Language, Inc.)
license: CC BY 4.0
tier: 1
tags: [evans, canonical, reference, foundational]
ingested: 2026-04-19
vendored_from: domainlanguage.com DDD Reference PDF (Jun 2015)
adaptation_notes: Condensed summary of Evans' 2015 Reference (the official canonical pattern glossary). CC BY 4.0 permits derivative works with attribution. This is a *summary for citation purposes*; skills should cite this file when invoking Evans' core patterns.
---

# Evans DDD Reference — Condensed

This file summarises the pattern vocabulary from Eric Evans' 2015 *Domain-Driven
Design Reference* — the authoritative definitions of DDD's core patterns, more
current than the 2003 book.

When skills cite "Evans says X", they must cite **this file** with the pattern
name — e.g. `per kb/canonical/evans-ddd-reference.md § Bounded Context`.

## Part I — Putting the Domain Model to Work

### Ubiquitous Language
A language structured around the domain model, used by all team members
(developers, domain experts, product) in all communication (conversation,
code, diagrams, docs). The vocabulary includes names of classes and prominent
operations. Terms must be used consistently — synonyms are forbidden within a
single Bounded Context.

> "The language changes when the model changes; the model changes when the
> language changes." — core feedback loop.

### Model-Driven Design
Binding code and model tightly — the code **is** the model's expression, not a
translation of it. If a rename in the domain conversation doesn't trigger a
rename in the code, the model has split.

### Hands-On Modellers
Senior engineers must stay close to the modelling conversation. Modelling
done by architects who don't write code produces models that don't survive
contact with implementation.

## Part II — Building Blocks (Tactical Patterns)

> ⚠️ Out of v1 scope for this agent (ideation-only), but named for completeness
> so the interrogator can push back on premature tactical discussion.

- **Entity** — has identity that persists beyond its attributes.
- **Value Object** — defined entirely by its attributes; immutable;
  interchangeable with any value-equal instance.
- **Aggregate** — cluster of entities/VOs with a root, treated as a
  consistency boundary. See `kb/patterns/aggregate-design-canvas.md`.
- **Domain Event** — something meaningful that happened in the domain.
- **Service** — a stateless operation that doesn't naturally belong to an
  entity or VO.
- **Repository** — abstraction over storage of aggregates.
- **Factory** — encapsulates complex object construction.

## Part III — Supple Design

Patterns that make a model easier to evolve without fragmentation:

- **Intention-Revealing Interfaces** — operations named for *what* and *why*,
  not *how*.
- **Side-Effect-Free Functions** — computation separated from state change.
- **Assertions** — invariants made explicit in code and test.
- **Conceptual Contours** — the boundaries of model fragments should match the
  natural "joints" of the domain.
- **Standalone Classes** — classes minimally entangled with the rest of the
  model.
- **Closure of Operations** — operations whose arguments and return types are
  all in the same set (like arithmetic).

These are code-level craft, so out of v1 scope, but worth naming when the
interrogator encounters code-shaped questions.

## Part IV — Strategic Design

**This is the core of v1.** The strategic patterns define how multiple models
coexist.

### Bounded Context
A boundary (usually a subsystem, or the work of a particular team) within
which a particular model is defined and applicable. Inside the boundary, terms
have specific meaning; outside, they may mean something different.

> "A model makes sense only in a context. A bounded context makes the context
> explicit."

See also: `kb/canonical/fowler-bounded-context.md`.

### Context Map
A description of the Bounded Contexts and the relationships between them. See
`kb/patterns/context-mapping-patterns.md` for the full pattern catalogue (SK,
OHS, PL, ACL, Conformist, Partnership, Customer/Supplier, Separate Ways).

### Core Domain
The part of the domain that is the primary reason the organisation is building
custom software — its competitive differentiator. Deserves the best modelling
effort and the best people. See `kb/patterns/core-domain-chart.md`.

### Subdomain types
- **Core Domain** — differentiator; invest heavily.
- **Supporting Subdomain** — necessary but not distinguishing; build simply or
  outsource.
- **Generic Subdomain** — common capability; **buy don't build** (auth,
  billing, CRM, etc.).

### Domain Vision Statement
A short (1 paragraph) shared statement of the core domain's value and scope —
a compass for later modelling work.

### Highlighted Core
Explicitly calling out which parts of documentation/diagrams describe the Core
Domain so attention isn't diluted across equally-emphasised supporting detail.

### Large-Scale Structure
A pattern applied across the whole system to let team members understand parts
in context — responsibility layers, evolving order, system metaphor.

### Distillation
Separating the truly differentiating part (Core) from supporting concerns, so
the Core can evolve without being buried.

## Crucial rules to remember

1. **"Whenever two models with different definitions of the same terms are
   combined, software becomes buggy, unreliable, and difficult to understand."**
   — Evans, on the necessity of Bounded Contexts.
2. **"The ubiquitous language is the language of the model."** — if a word
   used in the team conversation doesn't appear in the code (or vice versa),
   investigate.
3. **"The Core Domain is where the most value is to be added in your
   project."** — rigor is *unevenly distributed*; Core gets the most.
4. **Do not try to model everything.** Subdomains that are Supporting or
   Generic can be simple, crude, or bought.

## How this file should be cited

Skills cite this as the authoritative source for Evans' terminology. Example:

> Per `kb/canonical/evans-ddd-reference.md § Bounded Context`, a bounded
> context is a boundary within which a particular model is defined and
> applicable.

Do **not** attribute ideas to Evans that are not in this file. Plenty of
"Evans said" claims online are folklore — only cite what's documented here.

## Related references

- The full 2015 Reference PDF: https://www.domainlanguage.com/ddd/reference/
- Evans' 2003 original: *Domain-Driven Design: Tackling Complexity in the
  Heart of Software* (Addison-Wesley). The 2015 Reference supersedes its
  pattern definitions.
