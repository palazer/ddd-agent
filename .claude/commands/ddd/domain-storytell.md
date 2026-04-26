---
description: Run a Domain Storytelling session directly — narrative elicitation for workflow-heavy flows. Complement to Event Storming.
argument-hint: "<scope description>" [--slug <project-slug>]
---

# /ddd:domain-storytell

Direct entry point into the `domain-storytelling` skill.

## Arguments

- `"<scope description>"` — one specific story scope, not a whole process.
- `--slug <project-slug>` — optional; derived if omitted.

## Behaviour

1. Ensure `projects/<slug>/` exists (create stub if not).
2. Invoke the `domain-storytelling` skill.
3. Append output to `projects/<slug>/3-domain-stories.md` (one story per section).
4. Invoke the `glossary-curator` agent for any new terms.

## When to pick this over Event Storming

See `kb/ceremonies/domain-storytelling.md § When to pick this ceremony`.
Use Domain Storytelling when **sequence and actors matter more than the
events themselves**.

## Skill invoked

`domain-storytelling` — canonical process in
`kb/ceremonies/domain-storytelling.md`.

## Output

`projects/<slug>/3-domain-stories.md` (append).

## Related commands

- `/ddd:event-storm` — the complementary ceremony.
- `/ddd:glossary` — capture new terms surfaced by the story.
