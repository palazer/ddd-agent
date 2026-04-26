---
description: Fill or refine a Bounded Context Canvas v5 for one candidate BC. Walks the 11 sections with interrogator-mode pushbacks.
argument-hint: "<bc-name>" [--slug <project-slug>]
---

# /ddd:bc-canvas

Direct entry point into the `bounded-context-canvas` skill.

## Arguments

- `"<bc-name>"` — the candidate Bounded Context's name (from
  `projects/<slug>/5-bc-candidates.md`).
- `--slug <project-slug>` — optional.

## Preconditions

- `projects/<slug>/5-bc-candidates.md` exists and lists this BC.
- `projects/<slug>/4-glossary.md` exists (may be seed only).

If preconditions missing, the skill will ask the user to complete them first.

## Behaviour

1. Load `projects/<slug>/5-bc-candidates.md` to find the candidate.
2. Invoke the `bounded-context-canvas` skill for that BC.
3. Walk all 11 canvas sections with checkpointing.
4. Interrogator pushbacks:
   - Multiple archetypes on one BC → smell.
   - Brain Context shape → anti-pattern.
   - Technical language in Purpose → push back.
   - Everything Core → suspect.
   - No open questions → under-explored.
5. For new terms surfaced in § 7 → invoke `glossary-curator`.

## Skill invoked

`bounded-context-canvas` — canonical definition of all 11 sections in
`kb/ceremonies/bounded-context-canvas.md`.

## Output

`projects/<slug>/6-contexts/<bc-slug>.md`.

## Related commands

- `/ddd:example-map` — for vague business-decisions sections.
- `/ddd:glossary` — for term curation.
- `/ddd:ideate` — the orchestrator that produces the BC list.
