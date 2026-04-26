---
description: Run a 20–30 minute Example Mapping session for one story — separates rules, examples, and open questions.
argument-hint: "<story description>" [--slug <project-slug>]
---

# /ddd:example-map

Direct entry point into the `example-mapping` skill.

## Arguments

- `"<story description>"` — one user story, one sentence. If it takes >30min
  to map, the skill will push back for a split.
- `--slug <project-slug>` — optional.

## Behaviour

1. Ensure `projects/<slug>/3b-example-maps/` exists.
2. Invoke the `example-mapping` skill.
3. Write output to
   `projects/<slug>/3b-example-maps/<story-slug>.md`.
4. After completion, suggest flowing rules into the relevant BC canvas § 8
   and open questions into § 11.

## Use cases

- A hotspot from Event Storming is about *business rules* (not flows).
- A BC canvas's "Business Decisions" section is vague.
- An ADR under draft depends on a rule you can't illustrate.

## Skill invoked

`example-mapping` — canonical process in
`kb/ceremonies/example-mapping.md`.

## Output

`projects/<slug>/3b-example-maps/<story-slug>.md`.

## Related commands

- `/ddd:bc-canvas` — where rules flow.
- `/ddd:event-storm` — where hotspots that call for this are surfaced.
