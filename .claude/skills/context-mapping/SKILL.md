---
name: context-mapping
description: Draft a Context Map for candidate Bounded Contexts. Names every edge with both an organisational relationship (P/CS/CF) and an integration pattern (SK/OHS/PL/ACL/CF/SW). Writes to projects/<slug>/7-context-map.md.
argument-hint: [--slug <project-slug>]
allowed-tools: Read, Write, Edit, Glob, Grep, Skill, Agent
adapted_from: melodic-software/claude-code-plugins — plugins/enterprise-architecture/skills/context-mapping (MIT). CML syntax references removed (ContextMapper is deferred to v2). Patterns and labelling discipline retained.
---

# Context Mapping

Facilitator skill for step 7 of `/ddd:ideate` — drafting the Context Map of
inter-BC relationships.

**Canonical reference:** `kb/patterns/context-mapping-patterns.md`.

## Preconditions

- `projects/<slug>/5-bc-candidates.md` exists (step 5 of ideation) — this is
  the list of nodes.
- At least one Bounded Context Canvas filled for major BCs — gives inbound /
  outbound communication hints.

If either is missing, decline and ask the user to do them first.

## Execution

For each **pair** of candidate BCs:

1. **Ask: do they exchange information at all?** If no → no edge.
2. If yes, elicit three things in order:
   - **Organisational relationship:** Partnership | Customer/Supplier |
     Conformist | (Free if no exchange).
   - **Integration pattern:** SK | OHS | PL | ACL | Conformist | Separate
     Ways | Big Ball of Mud.
   - **Rationale** — one sentence on *why* this pair chose this combination.
3. Label the edge with the shorthand.

For each pair involving an **external system**: default to **ACL** unless the
user argues otherwise. Name the external system as its own node.

## Interrogator behaviours (from kb/patterns/context-mapping-patterns.md + voices/khononov)

- **Shared Kernel proposal → push back hard.** Cite
  `kb/voices/khononov-balancing-coupling.md`. SK is strong coupling;
  alternatives (OHS/PL) usually give same collaboration at lower pain.
- **Conformist with in-house upstream → suspicious.** CF is for vendors and
  legacy. In-house peers almost always mean ACL, not CF.
- **No ACL anywhere → check legacy/vendor edges.** Missing ACLs are a common
  failure.
- **ACL everywhere → consolidate.** A hidden OHS/PL opportunity is likely.
- **No Separate Ways on the map → probe.** "Is there a pair here that could
  just not integrate?"

## Output artifact — `projects/<slug>/7-context-map.md`

```markdown
# Context Map — <project name>

**Date:** <YYYY-MM-DD>
**Input:** projects/<slug>/5-bc-candidates.md
**References:** kb/patterns/context-mapping-patterns.md, kb/voices/khononov-balancing-coupling.md

## Nodes

### Internal Bounded Contexts
| BC | Archetype(s) | Core / Support / Generic |
|---|---|---|
| Pricing-Rules | Specification | Core |
| Pricing-Engine | Execution | Core |
| Billing | Execution | Supporting |
| ... | | |

### External systems
- PaymentGateway (third-party SaaS)
- ...

## Edges

| From | Relationship | Integration | To | Rationale |
|---|---|---|---|---|
| Pricing-Engine | Customer (downstream) | ACL | Pricing-Rules (upstream) | Engine isolates itself from rule-schema churn |
| Billing | Conformist | ACL | PaymentGateway | Vendor; translate their model |
| Billing | Partnership | PL | Pricing-Engine | Co-evolve tariff schema |
| ContentIngest | Separate Ways | — | Reporting | Intentionally non-integrated — each solves its need locally |
| ... | | | | |

## Open questions

- Who owns the Published Language between Billing and Pricing-Engine?
- Does the ContentIngest team agree with Separate Ways, or do they expect
  future integration?

## Candidate ADRs to draft

- ADR-00X: Use ACL between Billing and PaymentGateway
- ADR-00X: Shared Kernel rejected for Pricing-Rules / Pricing-Engine — use PL
```

## Post-run suggestions

- Every edge that was contentious → draft an ADR.
- Any BC that appears in SK or CF → re-examine its canvas for whether the
  relationship is stable.

## References (from kb/)

- `kb/patterns/context-mapping-patterns.md` — full pattern catalogue.
- `kb/voices/khononov-balancing-coupling.md` — coupling dimensions powering
  pushbacks.
- `kb/voices/verraes-bc-not-1-on-1.md` — BC vs. subdomain vs. microservice.
- `kb/patterns/bounded-context-archetypes.md` — archetype labels for nodes.

## Attribution

Structure adapted from melodic-software/claude-code-plugins
(`plugins/enterprise-architecture/skills/context-mapping/`), MIT. CML /
ContextMapper syntax references removed — ContextMapper integration is
deferred to v2. Pattern catalogue and edge-labelling discipline retained.
