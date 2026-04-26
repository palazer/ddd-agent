---
name: example-mapping
description: Run a 20–30 minute Example Mapping session for one story — separates rules, concrete examples, and open questions. Writes outputs to projects/<slug>/3b-example-maps/<story-slug>.md.
argument-hint: <story-description> [--slug <project-slug>]
allowed-tools: Read, Write, Edit, Glob, Grep, Skill
---

# Example Mapping

Facilitator skill for rule-level clarification (invoked by `/ddd:example-map`
or as a follow-up from Event Storming hotspots).

**Canonical reference:** `kb/ceremonies/example-mapping.md`.

## When to run

- A hotspot from Event Storming involves **business rules** (not flows).
- A candidate BC's "Business Decisions" section in its canvas is vague.
- An ADR under draft depends on a rule you can't illustrate.

## Process (per kb/ceremonies/example-mapping.md)

1. **Yellow — state the story.** One sentence. If it takes >30min, split.
2. **Blue — list rules.** Business rules governing the story.
3. **Green — illustrate each rule with concrete examples.** Real inputs, real
   outputs.
4. **Red — park unanswerable things as questions.** Don't solve them.
5. **Self-rate confidence.** 👍 / 👐 / 👎 on readiness.

## Interrogator behaviours (NON-NEGOTIABLE)

- **Refuse abstract examples.** "some amount", "a recent order" → not an
  example. Demand concrete values.
- **Refuse rule-only cards.** Every blue rule must be backed by ≥1 green
  example.
- **Refuse to resolve red questions by guessing.** A red question stays red
  until someone who actually knows answers it.
- **Flag rule contradictions.** When two examples contradict, name it
  immediately — often a missed BC boundary.
- **Split stories >6 rules.** Too big for one map.

## Output artifact — `projects/<slug>/3b-example-maps/<story-slug>.md`

```markdown
# Example Map — <story title>

**Date:** <YYYY-MM-DD>
**Reference:** kb/ceremonies/example-mapping.md

## Story (yellow)
<one sentence>

## Rules (blue)
1. <rule>
2. <rule>

## Examples (green)
- **R1 happy:** <input> → <output>
- **R1 edge:** <input> → <output>
- **R2 happy:** <input> → <output>

## Open Questions (red — UNRESOLVED)
- <question> — owner: <who will find out>
- <question> — owner: <>

## Confidence: 👍 | 👐 | 👎

## Feeds into:
- Bounded Context Canvas § 8 (Business Decisions)
- Bounded Context Canvas § 11 (Open Questions) — copy the red cards over
```

## Post-run

- Copy rules into the relevant BC's canvas § 8 (if canvas exists).
- Copy open questions into canvas § 11.
- If rules contradict → **run again for the contradicting case** with a
  separate scope.

## References (from kb/)

- `kb/ceremonies/example-mapping.md` — canonical.
- `kb/ceremonies/bounded-context-canvas.md § 8` — where rules flow to.
