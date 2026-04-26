---
description: Orchestrated DDD ideation loop — Cynefin framing → Event Storming → (optional) Domain Storytelling → Ubiquitous Language → Candidate BCs → BC Canvases → Context Map. Produces numbered artifacts under projects/<slug>/.
argument-hint: "<description of the idea>"
---

# /ddd:ideate

Run the full 7-step ideation loop for a new project idea.

## Arguments

`"<description>"` — free text describing the idea. Optional but strongly
recommended. If missing, ask the user.

## Pre-invocation: source documents (optional)

If you have existing specs, research notes, or stakeholder docs you want
the ideation to consume, drop them into `projects/<slug>/input/` **before**
invoking this command. The facilitator scans that folder before Step 1
(Cynefin) and threads the contents into the framing prompt. The `input/`
folder is read-only to the agent — it never rewrites your source material.

## Behaviour

1. **Launch the `ddd-facilitator` agent** with the user's description as
   context. The facilitator:
   - Derives a project slug and confirms it with the user.
   - Creates `projects/<slug>/` with stub files.
   - Runs the 7 steps sequentially, checkpointing after each.
   - Routes to the `ddd-interrogator` agent for pushbacks.
   - Invokes the `glossary-curator` agent whenever new terms surface.

2. The **first gate** is Cynefin framing. If the domain is Clear or Chaotic,
   the facilitator may refuse to continue the full loop. Respect that.

3. The flow may pause at any step for user input — this is intentional. The
   ideation loop is a conversation, not a batch job.

## What the user should expect

| Step | What happens | Artifact |
|---|---|---|
| 1. Cynefin framing | 5 classification questions | `projects/<slug>/1-cynefin-framing.md` |
| 2. Event Storming | Past-tense event discovery | `projects/<slug>/2-event-storm.md` |
| 3. Domain Storytelling (optional) | Narrative elicitation | `projects/<slug>/3-domain-stories.md` |
| 3b. Example Mapping (optional) | Story-level rule clarification | `projects/<slug>/3b-example-maps/<story-slug>.md` |
| 4. Ubiquitous Language | Glossary curation | `projects/<slug>/4-glossary.md` |
| 5. Candidate BCs | Boundary drafting + Verraes/Khononov checks | `projects/<slug>/5-bc-candidates.md` |
| 6. BC Canvases | One per major BC | `projects/<slug>/6-contexts/<bc-slug>.md` |
| 7. Context Map + ADRs | Inter-BC relationships | `projects/<slug>/7-context-map.md`, `projects/<slug>/7-adr/NNNN-*.md` |

Between every step the facilitator calls `AskUserQuestion` with three
options: `Proceed`, `Revise current step`, `Pause and save`. The loop only
advances on an explicit `Proceed`.

## Interrogator behaviours (enforced throughout)

- Pushback on anti-patterns is **not optional** (CLAUDE.md §5).
- Tactical code generation is **refused** (CLAUDE.md §3).
- Every claim must cite a `kb/` path (CLAUDE.md §4).
- The user makes all boundary decisions (CLAUDE.md §1 / Plöd L3).

## Related commands

- `/ddd:revise` — after ideation, use this for any subsequent artifact edit
  that may affect downstream documents. Walks the artifact-impact matrix and
  propagates changes consistently.
- `/ddd:event-storm` — jump directly into step 2.
- `/ddd:domain-storytell` — jump directly into step 3.
- `/ddd:example-map` — rule-level clarification (step 4-adjacent).
- `/ddd:bc-canvas` — fill a canvas for one BC (step 6).
- `/ddd:glossary` — add or review glossary entries.
