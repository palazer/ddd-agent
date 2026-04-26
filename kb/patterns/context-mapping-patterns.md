---
title: Context Mapping Patterns
source: https://github.com/ddd-crew/context-mapping + Evans, "Domain-Driven Design" ch.14
author: Eric Evans (original), ddd-crew (visual refinements — Michael Plöd, Alberto Brandolini)
license: fair-use summary (Evans chapter) + CC BY 4.0 (ddd-crew refinements)
tier: 1
tags: [context-map, strategic, integration, relationships]
ingested: 2026-04-19
vendored_from: native
adaptation_notes: Synthesis of Evans chapter 14 + ddd-crew context-mapping repo. Canonical reference for the /ddd:ideate step 7 draft context map.
---

# Context Mapping Patterns

A **Context Map** is a strategic-design artifact that shows how multiple
Bounded Contexts relate — both **organisationally** (how teams interact) and
**integration-wise** (how models translate).

The patterns below are standard taxonomy. A context map usually names both the
*integration pattern* (technical) and the *organisational relationship*
(political).

## Organisational relationships (upstream/downstream)

| Pattern | Shorthand | Meaning |
|---|---|---|
| **Partnership** | P | Two contexts succeed or fail together. Teams coordinate. No upstream/downstream. |
| **Customer / Supplier** | C/S | Downstream customer has legitimate input on upstream supplier's priorities. Supplier serves them. |
| **Conformist** | CF | Downstream has no leverage — must accept upstream model as-is. Often when upstream is a vendor or legacy system. |
| **Free** (implicit) | — | Two contexts don't interact. |

## Integration patterns (technical shape of the boundary)

| Pattern | Shorthand | Meaning | When to pick |
|---|---|---|---|
| **Shared Kernel** | SK | Two teams share a small, jointly-owned model fragment. Changes need mutual agreement. | Tightly-coupled, cohesive subdomains with high overlap. **Expensive — default to avoid.** |
| **Customer / Supplier** | C/S (also used here) | Upstream provides; downstream asks for what it needs. | Standard internal two-team integration. |
| **Open Host Service** | OHS | Upstream publishes a stable, well-documented protocol usable by many downstreams. | When multiple downstreams consume the same source. |
| **Published Language** | PL | A well-documented shared language (often paired with OHS) used for exchange — e.g. a well-versioned event schema. | Anywhere a stable vocabulary is needed at the boundary. |
| **Anticorruption Layer** | ACL | Downstream translates between its model and upstream's, to prevent upstream concepts leaking in. | **Default for any integration with legacy / vendor / misaligned upstream.** |
| **Conformist** | CF | Downstream adopts upstream's model wholesale, no translation. | When ACL cost is too high *and* model mismatch is acceptable. |
| **Separate Ways** | SW | Two contexts deliberately don't integrate — each solves its problem locally. | When integration cost exceeds integration benefit. Under-used. |
| **Big Ball of Mud** | BBoM | Recognising that an existing context is a tangled mess and should be **contained**, not untangled. | Common in brownfield. Naming it prevents scope creep. |

## The essential heuristic

> **Every arrow on a context map must name both an organisational relationship
> *and* an integration pattern.**

"Context A → Context B" with no label is useless. "Context A (downstream,
Conformist) — ACL — Context B (upstream)" tells you (a) the political reality,
(b) the technical shape.

## How to draft a context map (step 7 of /ddd:ideate)

1. List every candidate BC from step 5.
2. For each pair, ask: **do they need to exchange information?** If no → don't
   draw a line.
3. If yes → ask:
   - Who drives changes? (upstream/downstream)
   - Can we translate the upstream model, or do we conform? (ACL / CF)
   - Is the upstream's interface stable enough for multiple consumers? (OHS /
     PL)
   - Are both contexts equally coupled? (Partnership / Shared Kernel)
4. Label the edge with shorthand (e.g. `ACL`, `OHS/PL`, `CF`).
5. For any BC → external system boundary: **default to ACL**. Name the external
   system explicitly as its own node.

## Interrogator behaviour (Plöd-L3)

- If a user proposes **Shared Kernel** → push back *hard*. SK is expensive
  (requires two-team coordination on every change). Ask: could this be OHS/PL
  instead? Citing Khononov (`kb/voices/khononov-balancing-coupling.md`): SK is
  strong, short-distance coupling — minimise.
- If a user proposes **Conformist** with an in-house upstream → suspicious. CF
  is for vendors/legacy, not for peers. Usually the real answer is ACL or SK.
- If a user has **no ACL** between their context and a legacy/vendor system →
  push back. ACL is nearly mandatory at those boundaries.
- If **everything** on the map is ACL → the map is probably hiding a missing
  OHS/PL opportunity. Consolidate.
- If a user has not considered **Separate Ways** for any pair → prompt: "Is
  there a pair here that could just not integrate?"

## Output artifact

`projects/<slug>/7-context-map.md`:

```
## Nodes
- <BC name> — archetype (from kb/patterns/bounded-context-archetypes.md), core/supporting/generic
- ...
- <external system> — [external]

## Edges
- BC_A (downstream, Conformist) —[ACL]— BC_B (upstream)
- BC_B (upstream, Customer/Supplier) —[OHS + PL]— BC_C (downstream)
- BC_D —[Partnership]— BC_E
- BC_F —[Separate Ways]— BC_G  (intentionally non-integrated)

## Rationale notes
- Why ACL for A→B: legacy vendor, model mismatch on <X>
- Why SW for F↔G: integration cost > benefit; each domain solves locally
- Open question: who owns the Published Language between B and C?
```

## Common anti-patterns

1. **"All arrows no labels"** — a map with unlabelled edges is a box-and-line
   diagram, not a context map.
2. **SK everywhere** — teams trying to show cooperation by drawing SK
   everywhere. SK means *joint ownership of a model fragment*, which is
   rare and expensive.
3. **Missing external systems** — most maps under-count the real number of
   external boundaries. If you only have internal BCs on the map, ask what
   vendor/SaaS/legacy systems should be there.
4. **Direction ambiguity** — "A↔B" with no up/downstream is a smell. One team
   almost always drives.

## Related references

- Eric Evans, [Domain-Driven Design](https://www.domainlanguage.com/ddd/), ch. 14
- ddd-crew, [context-mapping](https://github.com/ddd-crew/context-mapping) (visual cheat sheets)
- Michael Plöd, [Visualising Sociotechnical Architectures with Context Maps](https://speakerdeck.com/mploed/visualizing-sociotechnical-architectures-with-context-maps)
- Vlad Khononov, [Balancing Coupling in Software Design](https://vladikk.com/) — rigorous treatment of when SK hurts.
