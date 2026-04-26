---
title: Bounded Context Canvas (v5)
source: https://github.com/ddd-crew/bounded-context-canvas
author: ddd-crew (Kenny Baas, Kim Lindhard, Michael Plöd, Maxime Sanglan-Charlier)
license: CC BY 4.0
tier: 1
tags: [bounded-context, canvas, strategic-design, design-level]
ingested: 2026-04-19
vendored_from: ddd-crew/bounded-context-canvas
adaptation_notes: Vendored verbatim from upstream README; trimmed image links to preserve plain-text ingestion.
---

# The Bounded Context Canvas

The Bounded Context Canvas is a collaborative tool for designing and documenting
the design of a single bounded context.

If you're not sure what a bounded context is, see Eric Evans' [DDD Reference](https://www.domainlanguage.com/ddd/reference/)
and [Martin Fowler's article](https://martinfowler.com/bliki/BoundedContext.html).

The canvas guides you through the process of designing a bounded context by
requiring you to consider and make choices about the key elements of its design,
from naming to responsibilities, to its public interface and dependencies.

## How to Use

> To quickly get started with the Bounded Context Canvas, complete the canvas
> in the order the sections are presented below.

Start with the name and description of the canvas to clarify its reason for
existing and key responsibilities in a sentence or two. Then you can fill in the
other sections of the canvas in any order. You could design outside-in starting
with inbound communication, or inside-out starting with the business rules and
domain language.

You may not have all the information you need to complete certain sections of
the canvas. In such a case, you'll need to use other modelling techniques to
find the information you require.

### Alternative Formats

- **Use Case Swimlanes** (Nick Tune): organizes the communication section into
  swimlanes showing the sequence in which interactions occur, using the format
  *message in* → *decision(s) made* → *message(s) out*.

## Section Definitions

### 1. Name
Naming is hard. Writing down the name of your context and gaining agreement as a
team will frame how you design the context.

### 2. Purpose
A few sentences describing the why and what of the context **in business
language**. No technical details here.

Writing down the purpose forces you to articulate fuzzy thoughts and ensure
everybody in the team is on the same page. Describe the purpose from a business
perspective; you may also name key actors for whom the bounded context provides
value.

### 3. Strategic Classification

How important is this context to the success of your organisation?

- **Core domain** — a key strategic initiative
- **Supporting domain** — necessary but not a differentiator
- **Generic** — a common capability found in many domains

What role does the context play in your business model?

- **Revenue generator** — people pay directly for this
- **Engagement creator** — users like it but they don't pay for it
- **Compliance enforcer** — protects your business reputation and existence

How evolved is the concept (Wardley Maps)?

- **Genesis** — new unexplored domain
- **Custom built** — companies are building their own versions
- **Product** — off-the-shelf versions exist with differentiation
- **Commodity** — highly-standardised versions exist

For deeper analysis, see `kb/patterns/core-domain-chart.md`.

### 4. Domain Roles

How can you characterise the behaviour of this bounded context? Does it receive
high volumes of data and crunch them into insights (an **analysis** context)?
Or does it enforce a workflow (an **execution** context)? Identifying the
different roles a context plays can help avoid coupling responsibilities.

See `kb/patterns/bounded-context-archetypes.md` for the full archetype list
(Specification, Execution, Analysis/Audit, Approver, Enforcer, Gateway, Bubble,
Brain, Funnel, Engagement, etc.).

### 5. Inbound Communication

Inbound communication represents collaborations that are initiated by other
collaborators.

**Messages.** Messages are the information one collaborator sends to another.
Three conversation types:

- **Command** — a request to do something
- **Query** — a request for some information
- **Event** — a notification that something has happened

The word *message* is used generally and is not tied to any implementation.
No message bus or asynchronous workflow is obligatory (a command, for example,
could simply be an HTTP POST).

**Collaborators.** Other systems or sub-systems that send messages to this
context — other bounded contexts, frontends, or external systems. If the BC
owns the user interface (e.g. micro-frontend), the collaborator type is
*direct user interaction*.

**Relationship Type.** The relationship type between two bounded contexts
indicates how the models and teams influence each other. See
`kb/patterns/context-mapping-patterns.md`.

**Swimlanes.** Collaborators can be organised into horizontal swim lanes
showing the messages they send.

### 6. Outbound Communication

Outbound communication represents collaborations that are initiated by this
context to interact with other collaborators. Same message types and notations
apply as inbound.

### 7. Ubiquitous Language

What are the key domain terms that exist within this context, and what do they
mean? This section is **sacred** — it seeds `projects/<slug>/4-glossary.md`.

### 8. Business Decisions

What are the key business rules and policies within this context?

### 9. Assumptions

You will never make design decisions having full knowledge of everything in
your domain. Most design happens based on assumptions; **make them explicit**
here.

### 10. Verification Metrics

DDD is iterative based on continuous learning. Metrics help gather valuable
input for those learnings. Think about metrics that you and your team can
define to gather learnings about whether the chosen boundaries are a good fit.

You can collect metrics from CI/CD environments, tools like JIRA, or live
systems.

### 11. Open Questions

If you have questions that no one in the room can answer while running a
workshop, enter them here. This way no open questions get lost, and you get a
visual indicator of how certain the team is regarding the design of a given
bounded context. Many questions are a good indicator of a high degree of
uncertainty — a useful signal for the interrogator.

## Design Tips

By making the important elements of a BC's design visible on the canvas, you
can more easily challenge and improve the design.

### General Tips

1. Experiment by moving something on the canvas to another context. How is the
   design affected?

### Interface Design Tips

The public interface of a bounded context is its contract with the rest of the
system. Contracts have a big impact on collaborators and are hard to change, so
good design is vital.

1. Are the names of messages coherent with each other and the description of
   the context?
2. Is each message type optimal (e.g. should a command be an event)?
3. Is the interface too big (too many unique message types)?
4. Is the context exposing too much of its internals?
5. Do any messages seem like they should belong elsewhere?

## Related references

- Nick Tune's [Bounded Context Canvas v3 additions](https://medium.com/nick-tune-tech-strategy-blog/bounded-context-canvas-v2-simplifications-and-additions-229ed35f825f)
- Xebia's [Extending the BC Canvas with BDD Examples](https://xebia.com/blog/extending-the-bounded-context-canvas-with-bdd-examples/)
