---
name: ubiquitous-language
description: Maintain and grow the project glossary (projects/<slug>/4-glossary.md). Detects synonyms, homonyms across BCs, and term drift. Invoked after any ceremony that surfaces new terms.
argument-hint: [--slug <project-slug>] [--term <term>]
allowed-tools: Read, Write, Edit, Glob, Grep, Skill, Agent
---

# Ubiquitous Language

Glossary-curation skill invoked by `/ddd:glossary` or proactively after
ceremonies (event-storming, domain-storytelling, example-mapping, bc-canvas).

**Canonical reference:** `kb/canonical/fowler-ubiquitous-language.md`.

## Purpose

Per CLAUDE.md hard rule #2, the Ubiquitous Language is **sacred**. Once a
term is defined in a project's glossary, it must be used verbatim. This
skill's job:

- Append new terms with clear, business-language definitions.
- Detect and flag **synonyms** (two terms, same concept → pick one).
- Detect and flag **homonyms across BCs** (same term, different meaning in
  two BCs → mark both explicitly).
- Detect **term drift** (the definition used in a ceremony contradicts the
  recorded definition).

## Preconditions

`projects/<slug>/4-glossary.md` exists. If not, create with the header:

```markdown
# Ubiquitous Language — <project name>

**Per CLAUDE.md §2**, terms here are authoritative — use verbatim in all
ceremonies, artifacts, and code.

**Per `kb/canonical/fowler-ubiquitous-language.md`**, scope is
per-bounded-context. Cross-BC homonyms are explicit, not hidden.

## Glossary entries

(See append order below.)
```

## Term entry format (per kb/canonical/fowler-ubiquitous-language.md § Practical glossary format)

```markdown
## <Term>

**Bounded Context:** <BC name | global>
**Definition:** <one sentence in business language>
**Examples:** <2–3 concrete examples>
**Synonyms to avoid:** <terms we are NOT using>
**Homonyms elsewhere:** <if this term means something different in another BC>
**First recorded:** <YYYY-MM-DD>
**Origin:** <which ceremony / source first surfaced this>
```

## Detection rules (interrogator)

### Synonym detection
When adding a new term, the skill greps the existing glossary for words with
similar meaning. If matched, **do not silently accept** — flag:

> "A term `<X>` is already recorded with meaning `<definition>`. Is the new
> term `<Y>` a synonym (in which case deprecate one) or a distinct concept
> (in which case explain the distinction)?"

### Homonym detection
When a term already exists in BC A and is being added for BC B, **check
whether the meanings match**:

- Same meaning → it's a global term; update BC scope.
- Different meaning → record both separately, with
  `Homonyms elsewhere:` cross-references.

### Term drift detection
When a ceremony output (event-storm, story, example-map, canvas) uses a
glossary term, check that the ceremony's implicit definition matches. If
mismatch:

> "The glossary records `<Term>` as `<definition>`. The ceremony uses it as
> `<different definition>`. Which is correct? Update the glossary or revise
> the ceremony output — don't leave them inconsistent."

## Interrogator behaviours

- **Refuse synonyms within one BC.** Cite
  `kb/canonical/fowler-ubiquitous-language.md`. Pick one term.
- **Require homonyms be explicit.** Cross-BC same-word-different-meaning is
  fine — but both glossary entries cross-reference each other.
- **Refuse technical jargon leaking in.** "DTO", "handler", "service" belong
  in code, not in the domain language. Push back.
- **Refuse vague definitions.** "The thing that manages orders" is not a
  definition. Demand precision or park as an open question.

## Output

Appended entries in `projects/<slug>/4-glossary.md`. No separate output file.

## References (from kb/)

- `kb/canonical/fowler-ubiquitous-language.md` — canonical definition + entry
  format.
- `kb/canonical/evans-ddd-reference.md § Ubiquitous Language` — foundational.
- `kb/canonical/fowler-bounded-context.md` — why scope is per-BC.
