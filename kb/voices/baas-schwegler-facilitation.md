---
title: Facilitation & Deep Democracy — Baas & Schwegler
source: DDD Europe workshops & talks, particularly "Collaborative Modelling" + "Visual Collaboration"
author: Kenny Baas-Schwegler (and co-authors, notably Evelyn van Kelle, Gien Verschatse)
license: fair-use summary
tier: 2
tags: [facilitation, collaboration, baas-schwegler, voices, deep-democracy]
ingested: 2026-04-19
vendored_from: native
adaptation_notes: Baas-Schwegler's body of work is primarily workshop-based. This file distils the published guidance on collaborative modelling facilitation.
---

# Baas-Schwegler — Facilitating Collaborative Modelling

Kenny Baas-Schwegler (DDD Europe regular, author, workshop facilitator) has
the most-developed body of guidance on **how to facilitate DDD ceremonies
well** — especially Event Storming and Domain Storytelling.

His key contribution to the canon is adapting **Deep Democracy** techniques
(Arnold & Amy Mindell) to technical modelling workshops. The core idea: the
**minority voice is usually where the undiscovered complexity hides**.

## Facilitation principles

### 1. **Seek the disagreement, don't suppress it**

When a workshop reaches "consensus" too quickly, the facilitator should be
suspicious. True consensus usually requires surfacing and working through
disagreement.

Techniques:
- **"Who sees this differently?"** as a regular prompt.
- Explicitly invite the person who hasn't spoken yet.
- **Name** the disagreement aloud when you notice it: "I see two different
  mental models here — let's name them before moving on."

### 2. **Make the implicit explicit**

Domain experts often know things they don't say, because they think everyone
knows. The facilitator's job is to draw these out.

Techniques:
- "Can you walk me through a concrete case?" (triggers story-mode, exposes
  tacit knowledge).
- "What would have to be true for this to fail?" (surfaces pre-conditions
  not explicitly stated).
- "If I were new to this team, what would surprise me?"

### 3. **Preserve dissent in the artifact**

Hotspots on an Event Storm, Red cards on an Example Map, Open Questions on a
BC Canvas — these aren't failures, they are the *most valuable data*. A
facilitator who "resolves" them by fiat destroys the signal.

Techniques:
- Physically record dissent on a distinct colour/shape of card.
- Refuse to let the conversation proceed without noting the dissent visibly.
- Schedule a follow-up for high-stakes disagreements rather than resolving
  them in the moment.

### 4. **Role-separate the facilitator from the expert**

Baas-Schwegler is emphatic: **the facilitator does not contribute
content**. They draw, prompt, and probe — but they do not propose the
answer. This is the same discipline that Tune's sparring stance requires
(`kb/voices/tune-strategic-sparring.md`).

If the same person has to be both facilitator and domain expert (small
teams), they should explicitly switch hats — "I'm stepping out of the
facilitator role to add a domain point; someone else keep me honest."

### 5. **Work in the visual**

Drawing is faster than talking. Baas-Schwegler frequently references Alberto
Brandolini's refrain: *"Modelling is thinking with your hands."*

- When verbal argument loops, move to paper/whiteboard.
- When paper argument loops, move to a concrete example.
- When concrete examples loop, split into two competing models and compare.

### 6. **Time-box and pulse**

Workshops fatigue people. Baas-Schwegler advocates 45-minute focused
intervals with breaks, and explicit "are we still on-track" check-ins every
60–90 minutes.

## Deep Democracy in the ceremony

The Mindells' Deep Democracy framework contributes two practices
Baas-Schwegler uses heavily:

### **Resistance as information**
When someone resists a proposal, the facilitator treats that resistance as
data about the *system*, not an obstacle to overcome. "What does your
resistance know that we haven't surfaced yet?"

### **Roles, not people**
Disagreements in workshops are often between *roles* (developer role vs.
product role vs. domain-expert role), not between *individuals*. Naming the
roles depersonalises the disagreement and lets it be worked through.

## Interrogator behaviour powered by this

- When a proposal faces one dissenting voice → **don't discount it**.
  Probe: "What are you seeing that the others aren't?"
- When consensus is fast and easy → **be suspicious**. Probe: "Who here
  would have drawn this differently a year ago?"
- When a sticky/card represents dissent → **preserve it visibly**. Do not
  absorb it into the consensus.
- Resist the urge to contribute content. When the user asks "what do you
  think?" → redirect to questions that surface the decision. Cite Tune.

## How this file should be cited

> Per `kb/voices/baas-schwegler-facilitation.md`, the facilitator preserves
> dissent rather than resolving it. This disagreement is information —
> let's record it as a hotspot and keep working.

## Related references

- Baas-Schwegler's DDD Europe talks (search "Kenny Baas" on YouTube).
- Baas-Schwegler, Evelyn van Kelle, et al., [Collaborative Software Design](https://www.manning.com/books/collaborative-software-design) (Manning, 2024).
- Mindell & Mindell, *Deep Democracy*.
- Brandolini, *Introducing EventStorming* (the facilitation chapters).
- `kb/voices/tune-strategic-sparring.md` — complementary facilitation stance.
