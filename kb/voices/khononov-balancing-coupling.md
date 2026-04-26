---
title: Balancing Coupling — Vlad Khononov
source: Khononov, "Balancing Coupling in Software Design" (Addison-Wesley, 2024) + talks
author: Vlad (Vladik) Khononov
license: fair-use summary
tier: 2
tags: [coupling, modularity, khononov, voices]
ingested: 2026-04-19
vendored_from: native
---

# Khononov — Balancing Coupling

Vlad Khononov's 2024 book *Balancing Coupling in Software Design* gives DDD
a rigorous coupling vocabulary that cuts through loose talk about "loose
coupling" and "high cohesion". It is quickly becoming the canonical reference
for thinking about *how tightly* two Bounded Contexts are bound.

## The three dimensions of coupling

Khononov argues that "coupling" isn't one thing — it's a combination of
three mostly-independent dimensions:

### 1. **Strength** — how intrusive is the coupling?
From least to most intrusive:

| Type | Example |
|---|---|
| **Intrusive** (shared memory) | Same process, shared mutable state. |
| **Functional** (shared function) | Shared library with behaviour (not just types). |
| **Model** (shared abstraction) | Two modules agree on a class/schema — `Shared Kernel`. |
| **Contract** (shared interface, stable) | Two modules agree on an API contract — `Published Language`. |

Stronger = more changes ripple; weaker = more independence.

### 2. **Distance** — how far apart are the coupled elements?
From closest to farthest:

| Distance | Example |
|---|---|
| Same class/module | Two methods in one class. |
| Same package/library | Two classes in one library. |
| Same service | Internal call graph. |
| Different services in same system | Microservice calls. |
| Across systems / organisations | Third-party APIs. |

Shorter = cheaper to change together; longer = changes are more expensive.

### 3. **Volatility** — how often do the coupled elements change?
Static vs. volatile. Two volatile things coupled strongly = pain.

## Khononov's core formula

**Coupling pain ≈ Strength × Volatility ÷ Distance**

- Two **stable** things coupled **strongly** across any distance → fine.
  (Think: `String` coupled to everything. It doesn't change, so strong
  coupling is free.)
- Two **volatile** things coupled **strongly** at **large distance** →
  disaster. (Microservices that need to rev together but are owned by
  different teams in different timezones.)
- Two **volatile** things coupled **strongly** at **short distance** →
  acceptable. (Two classes in one package that change together.)

## Why this matters for Bounded Contexts

Khononov reframes the BC boundary question:

> **"Two modules should be in the same Bounded Context if their coupling
> strength would be painful at larger distance — i.e., if they change
> together and the shared model is non-trivial."**

Conversely:

> **Two modules should be in separate BCs if the coupling strength is low
> OR if they change independently.**

This gives you a principled answer to "should these be one or two BCs?".

## Khononov on Shared Kernel

Shared Kernel (`kb/patterns/context-mapping-patterns.md`) is **Strength:
Model; Distance: variable; Volatility: usually high** — which gives high
coupling pain. SK is therefore a last resort for high-volatility, short-
distance cases only.

For most integrations → use Published Language (`Strength: Contract`),
which has dramatically lower pain for the same cross-BC distance.

## Khononov on Conformist

Conformist (`kb/patterns/context-mapping-patterns.md § Conformist`) is
**Strength: Model** with **Distance: far** (often across organisations).
Combined with **Volatility: upstream-controlled**, Conformist is often
unwittingly painful. Khononov's pushback: most Conformist relationships
should have an **Anticorruption Layer**, because the ACL reduces Strength
from Model to Contract — a major reduction in pain.

## Interrogator pushbacks powered by Khononov

- User wants to share a model class across two services → check Strength
  (Model → high) × Distance (far) × Volatility (?). If volatile, stop. Use PL.
- User draws two BCs but describes rules that change together → is this
  really two BCs, or one BC split by organisational convenience? Merge if
  coupling would be painful at this distance.
- User proposes a Shared Kernel between stable, non-volatile concepts →
  acceptable. (But re-check volatility assumption.)
- User wants to avoid ACL because "we own both sides" → check distance. If
  they're in separate services with separate deployment cadences, the
  distance matters more than the ownership.

## How this file should be cited

> Per `kb/voices/khononov-balancing-coupling.md`, the pain of a coupling is
> its *Strength × Volatility ÷ Distance*. What you're describing is high
> Strength + high Volatility at long Distance — that will hurt. Reduce one
> dimension.

## Related references

- Khononov, [Balancing Coupling in Software Design](https://www.amazon.com/Balancing-Coupling-Software-Design-Sustainable/dp/0137353480) (2024).
- Khononov, [Revisiting the Basics of DDD](https://vladikk.com/) — companion
  strategic-thinking essay.
- `kb/patterns/context-mapping-patterns.md` — the pattern catalogue that
  Khononov's dimensions score.
