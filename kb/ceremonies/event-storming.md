---
title: Event Storming (Big-Picture)
source: https://www.eventstorming.com/ + Brandolini "Introducing EventStorming" (Leanpub)
author: Alberto Brandolini
license: fair-use summary (concept described widely in CC material)
tier: 1
tags: [ceremony, discovery, events, big-picture, brandolini]
ingested: 2026-04-19
vendored_from: native
adaptation_notes: Our own condensed playbook. Not vendored from a single source — synthesizes Brandolini's book + public talks + ddd-crew discovery material. Tuned for the /ddd:event-storm skill.
---

# Event Storming — Big-Picture Flavour (for Ideation)

Event Storming is a **collaborative modelling workshop** invented by Alberto
Brandolini. Its superpower is compressing weeks of requirements analysis into a
few hours by forcing the whole group to externalise their thinking onto a wall
of sticky notes — in **chronological order of domain events**.

v1 of this agent runs the **Big-Picture** flavour only — the broadest level,
focused on discovering the landscape. Design-Level and Process-Level storms are
deferred to v2.

## Core notation (sticky colours — notional, we use markdown tokens)

| Element | Sticky | Markdown token | Rule |
|---|---|---|---|
| **Domain Event** | orange | `[EVENT]` | **Past tense**. Something that happened. "OrderPlaced", not "PlaceOrder". |
| **Command** | blue | `[COMMAND]` | Imperative. The trigger. "PlaceOrder". |
| **Actor** | yellow (small) | `[ACTOR]` | Who issued the command. |
| **Policy / Reaction** | lilac | `[POLICY]` | "Whenever X happens, do Y." Glue between events. |
| **External System** | pink | `[EXT]` | Something outside our control. |
| **Read Model / View** | green | `[READMODEL]` | Information an actor reads *before* issuing a command. |
| **Aggregate** | pale yellow (large) | `[AGGREGATE]` | Consistency boundary. **Only emerges late — do not start here.** |
| **Hotspot** | pink/red outlined | `[HOTSPOT]` | Unknown / disagreement / pain. **Valuable — do not resolve too fast.** |

## The invariant that makes it work

> **Events are written in the past tense and arranged on a timeline.**

Violations to push back on:
- Present-tense "Place Order" → that's a command, not an event.
- Future-tense "Will send email" → that's a policy.
- Nouns ("Order") → not an event; ask "what *happened* to the order?"

## Phases of a Big-Picture storm

### Phase 1 — Chaotic Exploration
Everyone writes domain events on stickies **in silence**. No order, no
filtering. Duration: 15–30 min. Goal: breadth.

**Facilitator's only job here:** enforce past-tense. Don't correct content, don't
cluster yet.

### Phase 2 — Enforce the Timeline
Walk the wall left-to-right. Duplicates get stacked. Arguments about ordering
become **hotspots** (red stickies) — they are not bugs, they are the most
valuable findings.

### Phase 3 — People & Systems
For each event: who triggered it (Actor), what system is involved (External
System). Still past-tense.

### Phase 4 — Pivotal Events
Mark events that represent **state transitions the business cares about** with
a vertical bar. These become natural candidates for context boundaries in the
next ceremony.

### Phase 5 — Walk-Through
Tell the story of the domain left to right. Missing events will surface. Add
them. Mark disagreements as hotspots.

## Output artifact

`projects/<slug>/2-event-storm.md` — markdown timeline with:

1. **Event catalog** (numbered, past-tense, grouped by pivotal events)
2. **Actor roster** with the events each actor triggers
3. **External system roster** with inbound/outbound events
4. **Hotspot log** — numbered list of every open question, disagreement, or
   "we don't know yet" surfaced during the storm
5. **Candidate pivotal events** — a short list of 3–7 events that feel like
   boundary markers (feeds into step 5 BC identification)

## Interrogator behaviour (Plöd-L3)

- If the user starts naming aggregates or tables → **push back**. Citing
  `kb/anti-patterns/premature-tactical-design.md` and Brandolini: "Aggregates
  emerge from events, not the other way around. Let's put aggregates aside
  until phase 4."
- If the user writes future/present-tense → **refuse to accept the sticky**.
  Ask them to restate in past tense. This is non-negotiable.
- If the user tries to resolve every hotspot immediately → **preserve them**.
  Hotspots are data. Say so.
- If the event wall has fewer than ~15 events → the domain either is genuinely
  simple (flag Cynefin-Clear; cite `kb/patterns/cynefin-when-to-ddd.md`) or the
  user is under-exploring. Probe which.
- If the event wall has more than ~80 events with no pivotal structure → the
  user is likely mixing domains. Propose splitting.

## Adjacent ceremonies

- If domain events are obvious but the *flow* is not → switch to Domain
  Storytelling (`kb/ceremonies/domain-storytelling.md`).
- If individual rules keep creating hotspots → extract them to Example Mapping
  (`kb/ceremonies/example-mapping.md`).

## Common anti-patterns in Big-Picture storms

1. **"Let's just list the screens"** — stakeholders default to UI. Redirect to
   "what *happened* that caused the screen to change?"
2. **Developer monopoly** — if only engineers are talking, the model will be
   tech-shaped. Pull in domain experts.
3. **Paving over hotspots** — seniors often resolve hotspots by fiat to "keep
   things moving". This destroys the most valuable output of the storm.

## Related references

- Alberto Brandolini, [Introducing EventStorming](https://leanpub.com/introducing_eventstorming)
- Nick Tune, [Multiple flavours of EventStorming](https://medium.com/nick-tune-tech-strategy-blog/multiple-flavours-of-eventstorming-4f2f4e9dc62d)
- Visual Collaboration Tools / Paul Rayner, [The EventStorming Handbook](https://leanpub.com/eventstormingHandbook)
