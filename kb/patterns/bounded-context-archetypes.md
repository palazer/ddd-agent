---
title: Bounded Context Archetypes (Model Traits Worksheet)
source: https://github.com/ddd-crew/bounded-context-canvas/blob/master/resources/model-traits-worksheet.md
author: Michael Plöd, ddd-crew
license: CC BY 4.0
tier: 2
tags: [bounded-context, archetype, domain-role, strategic-design]
ingested: 2026-04-19
vendored_from: ddd-crew/bounded-context-canvas
adaptation_notes: Vendored verbatim from upstream model-traits-worksheet.md. A model is typically described by several traits — pick the ones that fit or invent your own.
---

# Bounded Context Archetypes (Model Traits)

A bounded context is typically described by a number of different **traits** or
**archetypes**. Review the list and choose which applies to the context you're
working on — or think of your own traits.

| Trait | Heuristic |
|---|---|
| **Specification / Draft Model** | Produces a document describing a job or request that needs to be performed. *Example:* Advertising Campaign Builder. |
| **Execution Model** | Performs or tracks a job. *Example:* Advertising Campaign Engine. |
| **Analysis / Audit Model** | Monitors execution. *Example:* Advertising Campaign Analyser. |
| **Approver** | Receives requests and determines whether they should progress to the next step. *Example:* Fraud Check. |
| **Enforcer** | Ensures that other contexts carry out certain operations. *Example:* GDPR Context (ensures other contexts delete all of a user's data). |
| **Octopus Enforcer** | Ensures that multiple / all contexts in the system comply with a standard rule. *Example:* GDPR Context (as above, but wider reach). |
| **Interchanger** | Translates between multiple ubiquitous languages. |
| **Gateway** | Sits at the edge of a system and manages inbound and/or outbound communication. *Example:* IoT Message Gateway. |
| **Gateway Interchange** | Combination of a gateway and an interchange. |
| **Dogfood Context** | Simulates the customer experience of using the core bounded contexts. *Example:* Whitelabel music store. |
| **Bubble Context** | Sits in front of legacy contexts providing a new, cleaner model while legacy contexts are being replaced. |
| **Autonomous Bubble** | A Bubble Context with its own data store, synchronising data asynchronously with the legacy contexts. |
| **Brain Context** *(likely anti-pattern)* | Contains a large number of important rules, and many other contexts depend on it. *Example:* a rules engine containing all the domain rules. |
| **Funnel Context** | Receives documents from multiple upstream contexts and passes them to a single downstream context in a standard format (after applying its own rules). |
| **Engagement Context** | Provides key features that attract users to keep using the product. *Example:* Free Financial Advice Context. |

## How the interrogator should use this

- Ask the user to classify each candidate BC by one or more archetypes. If they
  can't, the BC may not yet have a clear purpose.
- **Brain Context is flagged as a likely anti-pattern.** If a candidate BC has
  many dependencies pointing into it and contains most of the business rules,
  push back hard — propose decomposition.
- **Multiple archetypes on one BC** is often a smell — e.g., a context that is
  both Specification *and* Execution *and* Analysis is probably three BCs
  wearing one mask.
- **Bubble / Autonomous Bubble** archetypes are especially useful for brownfield
  conversations — flag them proactively when the user mentions legacy.

## Related references

- Alberto Brandolini's [Bounded Context Archetypes](https://medium.com/@cyrillemartraire/collaborative-construction-by-alberto-brandolini-an-archetype-of-bounded-contexts-bea640bbb5b) — original inspiration.
- Rebecca Wirfs-Brock's [Object Role Stereotypes](http://www.wirfs-brock.com/PDFs/A_Brief-Tour-of-RDD.pdf) — related but at a finer grain (object-level).
