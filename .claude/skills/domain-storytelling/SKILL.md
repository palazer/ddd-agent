---
name: domain-storytelling
description: Facilitate a Domain Storytelling session — actors, activities, and work objects in numbered narrative order. Writes per-story outputs to projects/<slug>/3-domain-stories.md.
argument-hint: <scope-description> [--slug <project-slug>]
allowed-tools: Read, Write, Edit, Glob, Grep, Skill, Agent
adapted_from: melodic-software/claude-code-plugins — plugins/enterprise-architecture/skills/domain-storytelling (MIT). Structure retained; simulated narration replaced with user-driven elicitation.
---

# Domain Storytelling

Facilitator skill for narrative-shaped flows (step 3 of `/ddd:ideate`,
optional).

**Canonical reference:** `kb/ceremonies/domain-storytelling.md`.

## When this skill runs

- Invoked by `/ddd:domain-storytell` directly.
- Invoked from within `/ddd:ideate` when Event Storming reveals that **the
  sequence and actors matter more than the events themselves**.
- Use the pick table in
  `kb/ceremonies/domain-storytelling.md § When to pick this ceremony` to
  decide whether storytelling or event storming is better for the situation.

## Process (per kb/ceremonies/domain-storytelling.md)

1. **Pick a scope** — one specific story, not a whole process. Axes:
   Pure-domain × Coarse preferred for ideation.
2. **Find a teller** — a domain expert who lived the story. Not a PM summary.
3. **Narrate; skill draws** — the user speaks, the skill captures activities
   in numbered order. Everyone else is silent.
4. **Play it back** — read the numbered diagram aloud. Expert corrects.
5. **Discover more stories** — variants, adjacent scopes, different
   perspectives. Each is a new section in the file.
6. **Harvest the ubiquitous language** — surface new terms to the
   `glossary-curator` agent.

## Interrogator behaviours (from kb/ceremonies/domain-storytelling.md)

- **Refuse to record abstractions.** "typically", "usually", "it depends" →
  park and ask for one concrete case.
- **One story, one line.** Do not draw branches on a first-pass story.
- **Glossary conflicts surface immediately.** If the same work object appears
  under two names across stories, flag it.
- **Role names, not person names.** "Dispatcher" not "John".

## Output artifact — `projects/<slug>/3-domain-stories.md`

Append a new section for each story:

```markdown
## Story: <title>

**Scope:** <Pure-domain | Digitalized> × <Coarse | Fine>
**Teller:** <role>
**Captured:** <YYYY-MM-DD>

### Actors
- <name> (<role>)
- ...

### Work Objects
- <object> — brief description
- ...

### Numbered Activities
1. <Actor A> <verb> <Work Object> to <Actor B>
2. <Actor B> <verb> <Work Object>
3. ...

### Open Questions
- <question>

### Glossary candidates (new terms to capture)
- <term>: <preliminary meaning>
```

## Post-run suggestions

- Every new term → invoke `/ddd:glossary` or the `glossary-curator` agent.
- Hotspots in narrative order → run `/ddd:example-map` for the ambiguous
  rules.
- Story coverage looks complete → proceed to candidate BCs.

## References (from kb/)

- `kb/ceremonies/domain-storytelling.md` — canonical process.
- `kb/ceremonies/event-storming.md` — complementary ceremony (same family).
- `kb/canonical/fowler-ubiquitous-language.md` — why harvest glossary
  entries.
- `kb/voices/baas-schwegler-facilitation.md` — facilitation stance.

## Attribution

Structure adapted from melodic-software/claude-code-plugins
(`plugins/enterprise-architecture/skills/domain-storytelling/`), MIT.
