---
title: DDD Starter Modelling Process (8 steps)
source: https://github.com/ddd-crew/ddd-starter-modelling-process
author: ddd-crew (Ciaran McNulty, Eduardo da Silva, Gien Verschatse, James Morcom, Maxime Sanglan-Charlier)
license: CC BY 4.0
tier: 1
tags: [process, strategic-design, iterative, starter, framework]
ingested: 2026-04-19
vendored_from: ddd-crew/ddd-starter-modelling-process
adaptation_notes: Vendored from upstream README. This is the **master backbone** of our /ddd:ideate orchestrator; our 7-step ideation loop is a subset (we stop before Code).
---

# DDD Starter Modelling Process

A step-by-step guide for learning and practically applying each aspect of
Domain-Driven Design — from orienting around an organisation's business model
to coding a domain model.

> **This process is for beginners.** It is **not** a linear sequence of steps
> that you should standardise as a best practice. DDD is an *evolutionary
> design process* which necessitates continuous iteration on all aspects of
> knowledge and design. On a real project you'll often be jumping back and
> forth between these steps.

## When to Use

- Kicking off a **greenfield project** (our primary v1 use case)
- Beginning a **brownfield migration**
- Kicking off a major program of work
- Exploring your domain for new learning opportunities
- Assessing current state of your project
- Re-organising teams
- Practicing or learning DDD

## How to Adapt the Process

On a real project you'll switch between all 8 steps based on new insights.
Common deviations:

- **Start with Collaborative Modelling** if modelling the familiar domain is
  more comfortable than talking business strategy.
- **Start by Assessing IT Landscape** before looking forward — map out strategic
  portfolio first (start with step 5).
- **Code Before Confirming Architecture and Team Boundaries** if you need an MVP
  or the domain is complex enough that code is the best way to model.
- **Repeat Steps 2 (Discover) – 6 (Organise) Before Moving to 7 (Define)** —
  model the domain multiple times before diving into BC definitions.
- **Organise Teams Before Designing Contexts** when organisational constraints
  dominate.
- **Blending Definition and Coding** (steps 7 and 8 can occur concurrently).

## The 8 Steps

Grouped into four phases by Eduardo da Silva in
[Sociotechnical Architecture](https://www.youtube.com/watch?v=ekMPm78KFj0):

1. **Align & Understand**
2. **Strategic Architecture**
3. **Strategy & Org Design**
4. **Tactical Architecture**

### 1. Understand  *(Align & Understand)*

Align focus with the organisation's business model, user needs, short/medium/
long-term goals.

Every architecture/code/org decision has business and user consequences. Badly
designed architecture or boundaries can make business goals impossible.

**Recommended tools:**
- [Impact Mapping](https://www.impactmapping.org/)
- [Business Model Canvas](https://www.strategyzer.com/canvas/business-model-canvas)
- [Product Strategy Canvas](https://melissaperri.com/blog/2016/07/14/what-is-good-product-strategy)
- [Wardley Mapping](https://learnwardleymapping.com/)
- [User Story Mapping](https://www.jpattonassociates.com/user-story-mapping/)

**Who to involve:** software designers/builders/testers, domain experts,
product-strategy folks, real end users.

### 2. Discover  *(Strategic Architecture)*

Discover the domain visually and collaboratively. **The most crucial aspect of
DDD — you cannot skip discovery.** If the whole team doesn't build a good
understanding of the domain, all software decisions will be misguided.

> **Discovery is continuous.** Teams successful with DDD practice discovery
> frequently. There is always more to learn.

**Recommended tools:**
- [Domain Storytelling](https://domainstorytelling.org/) → `kb/ceremonies/domain-storytelling.md`
- [Example Mapping](https://cucumber.io/blog/bdd/example-mapping-introduction/) → `kb/ceremonies/example-mapping.md`
- [EventStorming](https://www.eventstorming.com/) → `kb/ceremonies/event-storming.md`
- User Journey Mapping, User Story Mapping

### 3. Decompose

Decompose the domain into sub-domains — loosely-coupled parts. Reasons:

- Reduced cognitive load
- Team autonomy
- Identifying loose-coupling and high-cohesion that carries over to architecture
  and team structure

**Recommended:** Carve up your event storm into sub-domains; use [Context Maps](https://speakerdeck.com/mploed/visualizing-sociotechnical-architectures-with-context-maps).

**Tools:**
- Business Capability Modelling
- [Design Heuristics](https://www.dddheuristics.com/)
- EventStorming with sub-domains
- [Independent Service Heuristics](https://github.com/TeamTopologies/Independent-Service-Heuristics)
- Visualising Sociotechnical Architecture with Context Maps

### 4. Strategize

Strategically map sub-domains to identify **core domains** — the parts with
greatest potential for business differentiation or strategic significance.

**Recommended:** [Core Domain Charts](../patterns/core-domain-chart.md).

**Tools:** Purpose Alignment Model, Wardley Mapping,
Khononov's "Revisiting the Basics of DDD".

### 5. Connect

Connect sub-domains into a loosely-coupled architecture fulfilling end-to-end
business use-cases. Challenge the initial design by applying concrete use-cases
to uncover hidden complexity.

**Recommended:** [Domain Message Flow Modelling](https://github.com/ddd-crew/domain-message-flow-modelling).

**Tools:** BPMN, Process Modelling EventStorming, Sequence Diagrams.

### 6. Organise  *(Strategy & Org Design)*

Organise autonomous teams optimised for fast flow and aligned with context
boundaries.

> **Team self-organisation.** Organisation is not something done *to* teams;
> teams should be involved in defining their own boundaries and interactions.

**Recommended:** Visualise sociotechnical architecture with
[Context Maps](https://speakerdeck.com/mploed/visualizing-sociotechnical-architectures-with-context-maps)
and [context-mapping](https://github.com/ddd-crew/context-mapping) patterns.

**Tools:** Dynamic Reteaming, Explorers/Villagers/Town Planners, Team Topologies.

### 7. Define  *(Tactical Architecture)*

Define roles and responsibilities of each bounded context. Make explicit
decisions about choices with significant impact, while it's still easy to
change your mind.

**Recommended:** [Bounded Context Canvas](../ceremonies/bounded-context-canvas.md).

**Tools:** Bounded Context Canvas, C4 System Context Diagram, Quality Storming.

### 8. Code

Code the domain model. **Out of v1 scope for this agent** — v1 ends at step 7.

**Recommended:** [Aggregate Design Canvas](aggregate-design-canvas.md).

**Tools:** Aggregate Design Canvas, C4 Component Diagrams, Design-Level
EventStorming, Event Modeling, Hexagonal Architecture, Mob Programming,
Model Exploration Whirlpool, Onion Architecture, UML.

## Relationship to Evans' Whirlpool Process

Both are continuous and iterative guides, not rigid best-practices. The Starter
Process covers more — aiming at a **sociotechnical architecture**. Evans'
Whirlpool remains totally relevant for exploring models within a step.

## How our /ddd:ideate orchestrator maps to this

| Starter Step | v1 /ddd:ideate step |
|---|---|
| 1. Understand | (light-touch — framed by user's initial prompt) |
| 2. Discover | Step 2 Event Storming, Step 3 Domain Storytelling, Step 4 Ubiquitous Language |
| 3. Decompose | Step 5 Candidate Bounded Contexts |
| 4. Strategize | Folded into Step 5 (Core Domain Chart reasoning) |
| 5. Connect | Step 7 Context Mapping draft |
| 6. Organise | Light-touch (flagged for v2 team-topology skill) |
| 7. Define | Step 6 Bounded Context Canvas per candidate BC |
| 8. Code | **Out of scope** for v1 |

Plus our Step 1: **Cynefin framing** (not in the Starter Process but critical
for refusing to apply DDD in Clear/Simple domains).
