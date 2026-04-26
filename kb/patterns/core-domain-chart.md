---
title: Core Domain Chart
source: https://github.com/ddd-crew/core-domain-charts
author: ddd-crew (Eduardo da Silva, primarily)
license: CC BY 4.0
tier: 1
tags: [core-domain, strategic-design, subdomain, differentiation, complexity]
ingested: 2026-04-19
vendored_from: ddd-crew/core-domain-charts
adaptation_notes: Vendored verbatim from upstream README.
---

# Core Domain Charts

Core Domain Charts help you visualise the **strategic importance** of each
(sub)domain or business capability in your architecture, allowing business-model-
aligned architectural decisions.

[Core Domains](https://www.youtube.com/watch?v=PBRluTD5oHo) are the parts of
your domain where the expected [ROI](https://www.investopedia.com/terms/r/returnoninvestment.asp)
is greatest, and deserve the highest focus.

The true power of this technique is the **conversations it triggers**,
especially cross-discipline. Complexity is something engineers can gauge;
business differentiation is provided by product managers or business
stakeholders.

Axes:
- **X (horizontal):** Market Differentiation (low → high)
- **Y (vertical):** Model Complexity (low → high)

The top-right quadrant (high complexity + high differentiation) = **Core
Domains** — focus your best talent there.

## How to Use

Don't try to visualise all possible information in a single diagram. Choose one
flavour.

### (Sub)Domain / Bounded Context Portfolio

The simplest flavour. Plot each (sub)domain or bounded context on the chart to
get a relative sense of ordering between them.

### Context Map With Team Topologies

Augment with dependencies between bounded contexts and the type of
[Team Topologies](https://github.com/TeamTopologies) interaction mode in play.

### Architecture Migration

Slight tweak of the y-axis label → use Core Domain Charts to plan the order in
which you migrate from current to target architecture.

## Suggestions for Measuring Complexity and Differentiation

Measuring complexity and differentiation is hard and usually quite subjective.
Strategy is a bet on the future — you can never be sure exactly how complex or
differentiating something will turn out. There is still a lot of value in
discussing and visualising your beliefs.

Clues for assessing each domain:

- How difficult is it to design a conceptual model for the domain and build
  (and maintain) it as software? (**essential domain complexity**)
- Is the current technical solution more complex than it needs to be?
  (**accidental technical complexity**)
- Are there complex processes, calculations, and decisions happening **outside**
  the software? (**operational complexity**)
- How hard would it be for a new entrant to match or exceed the capability?
- How hard would it be for an existing competitor to match or exceed the
  capability?
- How much of an advantage is currently derived from the (sub)domain (revenue,
  brand, engagement)?
- How much of an advantage *could* potentially be derived?
- What damage would occur to the brand if major or recurring failures happened
  in this domain?
- Which [Cynefin domain](https://cynefin.io/wiki/Cynefin_Domains) does it fall
  within? (See `kb/patterns/cynefin-when-to-ddd.md`.)

Dimensions of complexity to tease apart:

- How difficult is discovering potential new value?
- How difficult is designing the rules, logic, and workflows that create value?
- What level of scale does the system need to operate at? (even simple rules at
  scale add complexity)
- Are very high levels of differentiation/complexity needed to displace existing
  market leaders?
- Is specialist expertise/talent required that is difficult and expensive to
  acquire?
- How long does it take for a newcomer to ramp up?

## How the interrogator should use this

- **Core domain:** deserves deep modelling, expert team, invest in DDD rigor.
- **Supporting domain:** good enough software; team autonomy matters more than elegance.
- **Generic domain:** buy, don't build. Seriously push back on "we'll build it ourselves".

If a user labels everything as core, that's a signal the chart hasn't been
done honestly — *nothing* is differentiating if *everything* is differentiating.

## Related references

- [Core Domain Patterns (Nick Tune)](https://medium.com/nick-tune-tech-strategy-blog/core-domain-patterns-941f89446af5)
- [Visualising Sociotechnical Architecture with DDD and Team Topologies](https://medium.com/nick-tune-tech-strategy-blog/visualising-sociotechnical-architecture-with-ddd-and-team-topologies-48c6be036c40)
- [Process Automation Map (Bernd Rücker)](https://blog.bernd-ruecker.com/exploring-the-process-automation-map-7d9aa181a747) — complementary for process complexity.
- Inspired by [Wardley Mapping](https://medium.com/wardleymaps) and [Team Topologies](https://teamtopologies.com/).
