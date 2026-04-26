<!--
Thanks for sending a PR. Please fill in the sections below so review can be quick.
If anything below doesn't apply, write "n/a" rather than deleting the heading —
that way reviewers know you considered it.
-->

## What this changes

<!-- One-paragraph summary. What problem does this fix or what capability does it add? -->

## Why

<!-- The motivation. Link to an issue if there is one (e.g. "Fixes #12"). -->

## Kind of change

- [ ] Bug fix in an existing skill / agent / orchestrator
- [ ] New ceremony or skill (`.claude/skills/`)
- [ ] New agent or agent behaviour (`.claude/agents/`)
- [ ] Knowledge-base addition (`kb/canonical/`, `kb/voices/`, `kb/anti-patterns/`, `kb/patterns/`)
- [ ] Orchestrator change (`/ddd:ideate` or `/ddd:revise`)
- [ ] Documentation only (`README`, `CLAUDE.md`, `CONTRIBUTING.md`, `LICENSE`, etc.)
- [ ] Other (describe)

## Hard-rule check (per [`CLAUDE.md`](../blob/main/CLAUDE.md))

- [ ] Plöd-L3 ceiling (rule 1) — agent still sparrs, doesn't decide.
- [ ] Ubiquitous Language scoping (rule 2) — no project-specific terms baked into shared `kb/`.
- [ ] No tactical code generation (rule 3) — no aggregates, entities, framework code.
- [ ] Cite or don't claim (rule 4) — every new DDD assertion cites a `kb/` path or external source.
- [ ] Anti-patterns are pushed back, not tolerated (rule 5).
- [ ] Cascading revisions handled (rule 6) — if a term renames or a BC boundary shifts, the change was propagated via the artifact-impact matrix.

If any check fails, explain why below — sometimes a rule has a legitimate exception, but it must be argued, not assumed.

## Smoke test (per [`CONTRIBUTING.md`](../blob/main/CONTRIBUTING.md))

<!--
Required for changes that touch the orchestrator, a skill, or an agent.
Skip for kb/-only PRs that don't change any execution path.
-->

- [ ] Ran `/ddd:ideate "<tiny test domain>"` end-to-end against `main`-with-this-PR.
- [ ] All 7 steps emitted artifacts under `projects/<test-slug>/`.
- [ ] Each step gated on `AskUserQuestion` (Proceed / Revise / Pause).
- [ ] `glossary-curator` correctly flagged any synonyms / drift introduced.

Test domain used (or `n/a`):

```
<paste the description string you passed to /ddd:ideate>
```

Diff-stat of the resulting `projects/<test-slug>/` (or paste the tree):

```
<paste output of:  find projects/<test-slug> -type f | sort  ;  or `tree projects/<test-slug>`>
```

## Anything reviewers should look at first

<!-- Pointers to the most consequential file or trickiest hunk. Helps reviewers focus. -->
