---
title: Domain Storytelling
source: https://domainstorytelling.org/ + Hofer & Schwentner, "Domain Storytelling" (Addison-Wesley, 2021)
author: Stefan Hofer, Henning Schwentner
license: fair-use summary
tier: 1
tags: [ceremony, discovery, narrative, ubiquitous-language]
ingested: 2026-04-19
vendored_from: native
---

# Domain Storytelling

Domain Storytelling is a **graphical narrative technique** where domain experts
tell concrete stories about how work flows, and a modeller draws the story as a
pictographic sequence: **actors → activities → work objects → other actors**.

Compared to Event Storming, it excels when **the sequence and actors matter more
than the events themselves** — e.g. workflow-heavy domains, multi-actor
coordination, back-office processes.

## Core notation

| Element | Notation | Role |
|---|---|---|
| **Actor** | pictogram (person/system) with a name | Who does something. Use role names, not individuals. |
| **Work Object** | pictogram (document/thing) with a name | What moves through the story. |
| **Activity** | numbered arrow verb-phrase | What the actor does to the work object. **Numbered 1, 2, 3…** — narrative order is sacred. |
| **Annotation** | comment sticker | For conditions, variants, time gaps. |

## The invariant that makes it work

> **One concrete story at a time, in a single walkthrough, numbered in order.**

No branches, no "and sometimes", no "or else" in the first pass. Variants come
in *additional stories*, not as decorations on the first one.

## The process

### 1. Pick a scope
A specific *story*, not a whole process. Example: "A customer books a charging
session at a public station" — not "charging".

Scopes come in two axes:
- **Domain purity:** Pure-domain (ideal world) vs. Digitalized (current
  software involved).
- **Granularity:** Coarse (big picture) vs. Fine (one activity deep dive).

Ideation usually wants **Coarse + Pure-domain** first.

### 2. Find a domain expert who lived the story
Not a proxy, not a PM summary. Someone who did the work or watched it happen.

### 3. Let the expert narrate; the modeller draws
The expert speaks; the modeller draws activities numbered 1, 2, 3…. Everyone
else watches silently.

Facilitator's only job: keep the narrative **concrete and chronological**. When
the expert says "it depends" — stop, park it, continue with one branch.

### 4. Play it back
Read the diagram aloud. The expert confirms or corrects. Almost always
something changes.

### 5. Discover more stories
A single story is never enough. Common follow-ups:
- **Variant** of the same scope (happy vs. exception path).
- **Adjacent** scope (what happens before? after?).
- **Different actor's perspective** (same scope, different protagonist).

### 6. Harvest the ubiquitous language
Every word the expert used that named a work object, an actor role, or an
activity verb → candidate glossary entry. Feed to `glossary-curator`.

## Output artifact

`projects/<slug>/3-domain-stories.md` — one section per story:

```
## Story: <title>
**Scope:** <Pure-domain | Digitalized> × <Coarse | Fine>
**Teller:** <role>
**Captured:** <date>

Actors: Alice (Driver), CPO (Charging Operator), Station, Billing
Work Objects: Session Request, Charging Session, Invoice

1. Driver submits Session Request to CPO
2. CPO forwards Session Request to Station
3. Station returns Session Confirmation to Driver
4. ...

Open questions:
- What happens if the station rejects?
- Is the invoice generated per-session or batched?
```

## Interrogator behaviour (Plöd-L3)

- If the storyteller lapses into abstractions ("typically", "usually", "it
  depends") → **refuse to record** until a single concrete case is named. Park
  the variants.
- If the modeller starts drawing branches/decorations on a first-pass story →
  **push back**: "One story, one line. Variants are their own stories."
- If the same work object appears with different names across stories → **flag
  immediately** as a glossary conflict. Cite
  `kb/canonical/fowler-ubiquitous-language.md`.
- If an actor is named by person ("John") not role ("Dispatcher") → push for
  the role name.

## When to pick this ceremony over Event Storming

| Situation | Pick |
|---|---|
| Many parallel independent events; unclear which come first | Event Storming |
| Clear workflow with handoffs between actors | Domain Storytelling |
| UX / user-journey heavy | Domain Storytelling |
| Back-office / batch / integration heavy | Event Storming |
| Disagreement about whether something *happened* at all | Event Storming (hotspots surface) |
| Disagreement about who *did* what | Domain Storytelling |

Both ceremonies are **complementary**, not competing. Real projects use them
together.

## Common anti-patterns

1. **Committee storytelling** — five people trying to narrate one story at
   once. Pick one teller per story.
2. **Abstract narration** — "the system does X". Always ask *who* and *to
   what*.
3. **Diagramming tool obsession** — use whatever captures the narrative
   fastest; the artifact is the conversation, not the picture.

## Related references

- Hofer & Schwentner, [Domain Storytelling](https://domainstorytelling.org/)
  (book is the authoritative reference)
- [WPS Egon](https://egon.io/) — free web-based modeller (no account needed)
- [miro/Mural domain-storytelling templates](https://domainstorytelling.org/tools) — for remote workshops
