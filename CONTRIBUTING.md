# Contributing to ddd-agent

Thanks for your interest. This is a small, opinionated project — please read
this short note before opening a PR so we can keep the bar consistent.

## Philosophy boundary

This repo is a **DDD ideation sparring partner**, not a code generator. The
following are in-scope for v1:

- Strengthening the ideation ceremonies (Cynefin framing, Event Storming,
  Domain Storytelling, Example Mapping, BC Canvas, Context Mapping).
- Strengthening the diagram catalog and Mermaid templates
  (`kb/patterns/ddd-diagram-catalog.md`,
  `skills/diagram-catalog/templates/`).
- Adding to the curated knowledge base under `kb/` (canonical references,
  modern voices, anti-patterns, patterns).
- Sharpening the interrogator agent's pushbacks (`agents/`).
- Bug fixes in the orchestrator (`/ddd:ideate`, `/ddd:revise`,
  `/ddd:diagrams`) and skills.

The following are **explicitly out of scope** for v1 and will be declined:

- Tactical-code generation (aggregates, entities, value objects, framework
  scaffolding, jMolecules, ArchUnit). That's v2 territory.
- Diagram rendering backends beyond the Mermaid sources +
  `render.sh` hand-off — Structurizr DSL export, Kroki / server-side
  pipelines, and full C4 Levels 2–4 stay v2+.
- Full ContextMapper CML / MCP integration.
- Per-domain knowledge packs (industry-specific glossaries) — useful, but
  belongs in a separate companion repo, not the core.

See the [Deferred to later versions](README.md#deferred-to-later-versions)
list in the README for the full v2+ backlog.

## Hard rules (from `CLAUDE.md`)

These are non-negotiable for every PR. Each one has a number — please reference
it in your PR description when relevant:

1. **Plöd-L3 ceiling** — agents spar, they do not decide. PRs that let an
   agent silently make a domain-design choice without a checkpoint will be
   declined.
2. **Ubiquitous Language is project-scoped** — never bake project-specific
   terms into shared `kb/` files. Test glossaries belong under
   `projects/<your-slug>/4-glossary.md` (gitignored).
3. **No tactical code generation in v1** (see above).
4. **Cite or don't claim** — every DDD principle invoked in any output (skill
   prose, agent reasoning, kb/ entry) must cite a path under `kb/`. PRs that
   add unsourced claims will be rejected. "Evans said X" without a `kb/`
   citation is treated as a defect.
5. **Anti-patterns in `kb/anti-patterns/` are non-negotiable pushbacks.**
   Don't add anti-patterns the interrogator silently tolerates — they must be
   actively named and rejected when they appear.
6. **Cascading revisions are enforced, not remembered.** If your PR changes a
   term, an event name, a BC boundary, or a context-map edge, walk the
   artifact-impact matrix (`kb/patterns/artifact-impact-matrix.md`) and apply
   the change everywhere — or use `/ddd:revise` to do it for you.

The full hard-rule text lives in [`CLAUDE.md`](CLAUDE.md). Read it once before
your first PR.

## Smoke test before opening a PR

Run the orchestrator end-to-end against a tiny test domain:

```
claude
/ddd:ideate "tiny test domain — library that lets readers reserve study rooms"
```

Verify:

- The 7-step flow completes without errors.
- Each step writes a numbered artifact under `projects/<slug>/`.
- The facilitator calls `AskUserQuestion` between every step.
- The glossary builds incrementally (no terms invented, synonyms flagged).

Attach a diff-stat (or the resulting `projects/<slug>/` tree) to your PR
description so reviewers can see the smoke ran.

If your change is `kb/`-only and doesn't affect orchestration, run a more
targeted ceremony instead — e.g. `/ddd:bc-canvas` if you touched the BC
Canvas knowledge base.

If your PR touches `/ddd:diagrams`, the diagram catalog, or any file under
`skills/diagram-catalog/`, run the diagram skill against the same test slug
after the ideation completes:

```
/ddd:diagrams --slug <test-slug>
```

Verify the Phase 2 recommendation gate fires, the chosen diagrams emit
under `projects/<test-slug>/0-diagrams/`, and `render.sh` + `README.md`
are generated alongside them. If you touched a specific template, check
that its emitted `.md` carries the expected Mermaid type (lookup in
`kb/patterns/ddd-diagram-catalog.md`).

## When in doubt about scope

If your idea is bigger than a one-PR change, open an Issue first describing:

1. What problem you're trying to solve.
2. Which Plöd-L3 / hard-rule consideration it touches.
3. Where in the v1 / v2 split you think it lands.

That's a much cheaper conversation than a 500-line PR review.

## Code of conduct

This project follows the [Contributor Covenant v2.1](CODE_OF_CONDUCT.md).
Be kind. Disagree on ideas, never on people.

## License of contributions

By contributing, you agree your changes will be released under the same
dual-license as the rest of the project — **CC BY-SA 4.0** for additions
under `kb/` or `research/`, **MIT** for additions under `.claude/` or
elsewhere. See [`LICENSE`](LICENSE).
