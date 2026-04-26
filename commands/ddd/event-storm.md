---
description: Run a Big-Picture Event Storming session directly (jumps past Cynefin framing). Use when you already know the domain is Complex.
argument-hint: "<domain description>" [--slug <project-slug>]
---

# /ddd:event-storm

Direct entry point into the `event-storming` skill.

## Arguments

- `"<domain description>"` — the scope for the storm. If not provided, ask.
- `--slug <project-slug>` — optional; if omitted, derived from the
  description.

## Behaviour

1. If no `projects/<slug>/` exists yet, create the stub (same as
   `ddd-facilitator` would).
2. **Skip** the Cynefin gate — assume the user has already framed the
   domain. If the prompt obviously describes a Clear/Chaotic domain, warn and
   ask for confirmation before proceeding.
3. Invoke the `event-storming` skill with the provided description.
4. After completion, suggest follow-ups per
   `skills/event-storming/SKILL.md § Post-run suggestions`.

## Skill invoked

`event-storming` — canonical process in `kb/ceremonies/event-storming.md`.

## Output

`projects/<slug>/2-event-storm.md`.

## Related commands

- `/ddd:ideate` — orchestrated loop including Cynefin gate.
- `/ddd:domain-storytell` — narrative complement to event storming.
- `/ddd:example-map` — follow-up for hotspots about rules.
