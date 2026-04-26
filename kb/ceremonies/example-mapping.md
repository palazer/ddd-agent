---
title: Example Mapping
source: https://cucumber.io/blog/bdd/example-mapping-introduction/
author: Matt Wynne (original), popularised by Seb Rose, Gáspár Nagy
license: fair-use summary
tier: 1
tags: [ceremony, rules, examples, bdd, discovery]
ingested: 2026-04-19
vendored_from: native
---

# Example Mapping

Example Mapping is a **short, focused ceremony** (20–30 minutes) for clarifying
a single user story or business rule by separating four card types:

- **Yellow — Story/Feature** (one per session)
- **Blue — Rules** that govern the story
- **Green — Examples** that illustrate each rule (concrete, not abstract)
- **Red — Questions** that can't be answered in the room

It was invented by Matt Wynne as a lightweight precursor to writing BDD
scenarios, but it is extremely valuable in DDD ideation because **it
distinguishes policies from examples** — which is exactly where a lot of domain
confusion hides.

## The layout

```
                            [ YELLOW: Story ]
                                   |
         +-------------+-----------+-----------+-------------+
         |             |           |           |             |
   [ BLUE: Rule1 ] [ BLUE: Rule2 ] ...
         |             |
   [ GREEN Ex ]   [ GREEN Ex ]
   [ GREEN Ex ]   [ GREEN Ex ]

                     [ RED: Question ]  [ RED: Question ]
```

## The invariant that makes it work

> **Every blue rule must be backed by at least one green example, in concrete
> terms. Every abstract statement that can't be made concrete is either a red
> question or a hidden rule.**

When you can't write a green example for a rule, you don't understand the
rule. That's the signal.

## The process

### 1. State the story (yellow)
One sentence. "Customer applies for a refund." Keep it small — if it takes
longer than 30 min, split.

### 2. Brainstorm rules (blue)
What business rules govern this? "Refunds within 14 days are automatic." "No
refund if the item was used."

### 3. Illustrate each rule with examples (green)
**Concrete inputs → concrete outputs.** "Alice bought a book on Jan 1, asks
for refund Jan 10 (within 14 days, unused) → refund approved."

Rules without examples are suspect. Examples without rules are finding hidden
rules.

### 4. Park unanswerable things as questions (red)
Don't solve them. Park them. They drive the next conversation.

### 5. Self-rate confidence
Thumbs up/middle/down on whether the story is ready to work on. Many red
questions → not ready. Zero red questions → probably under-explored.

## Output artifact

`projects/<slug>/3b-example-maps/<story-slug>.md`:

```
## Story: <title>

### Rules
1. <rule>
2. <rule>

### Examples
- **R1 happy:** <input> → <output>
- **R1 edge:** <input> → <output>
- **R2 happy:** <input> → <output>

### Open Questions
- <question> (owner: <who will find out>)

### Confidence: 👍 | 👐 | 👎
```

## Interrogator behaviour (Plöd-L3)

- If the user offers an example with hand-waved inputs ("some amount", "a
  recent order") → **refuse**. Ask for concrete values.
- If the user adds rules without examples → **require at least one example**
  before moving on.
- If the user tries to collapse red questions by guessing → **push back**. A
  red question is only resolved by someone who actually knows.
- If the map has 6+ rules → the story is too big. Propose a split.
- If two rules contradict via their examples → flag explicitly; it's almost
  always a missed context boundary.

## When to run Example Mapping

- Hotspots surfaced in Event Storming that involve **business rules** (not
  flows).
- A candidate Bounded Context's "Business Decisions" section in its canvas is
  vague.
- An ADR is being drafted and the rationale depends on a rule you can't
  illustrate.

## How it feeds into downstream artifacts

- **Rules** → Bounded Context Canvas section 8 (Business Decisions).
- **Examples** → acceptance criteria / scenarios later, out of v1 scope.
- **Open questions** → Bounded Context Canvas section 11 (Open Questions) +
  ADR rationale sections.

## Common anti-patterns

1. **Rules in example language** — "the system checks if..." is how a rule
   *executes*, not what it *is*. Ask "what's the *policy*?"
2. **Examples in rule language** — "for any order" is not an example. Pin it
   to a specific order.
3. **Facilitator answering red questions** — the facilitator doesn't know.
   Park.

## Related references

- Matt Wynne, [Introducing Example Mapping](https://cucumber.io/blog/bdd/example-mapping-introduction/)
- Gáspár Nagy & Seb Rose, [Discovery: Explore behaviour using examples](https://bddbooks.com/)
- [Formulation (the sister technique)](https://cucumber.io/docs/bdd/better-gherkin/) — how examples become executable specs (out of v1 scope).
