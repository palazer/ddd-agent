---
description: Add, review, or reconcile glossary entries for a project. Detects synonyms, homonyms across BCs, and term drift.
argument-hint: [--slug <project-slug>] [--term <term>]
---

# /ddd:glossary

Direct entry point into the `ubiquitous-language` skill + `glossary-curator`
agent.

## Arguments

- `--slug <project-slug>` — required if multiple projects exist.
- `--term <term>` — optional; if provided, focuses on that term. Else, the
  skill audits the whole glossary and surfaces drift / conflicts.

## Behaviour

### Mode 1 — Add a term (when `--term` provided and not yet in glossary)
1. Invoke `ubiquitous-language` skill to walk the entry-format fields.
2. Append to `projects/<slug>/4-glossary.md`.

### Mode 2 — Review a term (when `--term` provided and already in glossary)
1. Read existing entry.
2. Invoke `glossary-curator` agent to check for drift across ceremony outputs.
3. Present reconciliation options to the user.

### Mode 3 — Audit (no `--term`)
1. Invoke `glossary-curator` agent.
2. Agent greps all `projects/<slug>/` numbered artifacts for glossary terms.
3. Reports synonyms, cross-BC homonyms not marked as such, and term drift.
4. Interactively walks through issues with the user.

## Rules (from CLAUDE.md §2)

- Glossary terms are used **verbatim**. No silent synonyms.
- Scope is per-BC (cross-BC homonyms explicit, not hidden).
- Technical jargon does not belong in the domain language.

## Skill / Agent invoked

- Skill: `ubiquitous-language`.
- Agent: `glossary-curator`.

## Output

`projects/<slug>/4-glossary.md` (append or edit).

## Related commands

- `/ddd:bc-canvas` — source of § 7 entries.
- `/ddd:event-storm`, `/ddd:domain-storytell`, `/ddd:example-map` —
  ceremonies that surface new terms.
