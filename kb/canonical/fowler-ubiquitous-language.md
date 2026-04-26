---
title: Ubiquitous Language (Fowler bliki)
source: https://martinfowler.com/bliki/UbiquitousLanguage.html
author: Martin Fowler
license: fair-use summary
tier: 1
tags: [ubiquitous-language, fowler, glossary, strategic-design]
ingested: 2026-04-19
vendored_from: native
adaptation_notes: Condensed summary of Fowler's bliki on Ubiquitous Language. The canonical short explanation of why naming matters.
---

# Ubiquitous Language (per Fowler)

A Ubiquitous Language is a **shared vocabulary** used across developers,
domain experts, product, and the code itself — structured around the domain
model. Fowler's bliki entry distils Evans' treatment into a one-page
explanation.

## Fowler's core points

### Why it matters
Without a shared language, conversations sprout translation overhead:
developers translate stakeholder-speak into "tech", and vice versa. Every
translation is a lossy compression step — bugs live in the losses. The
Ubiquitous Language eliminates the translator.

### What "ubiquitous" means
The *same* words, with the *same* meanings, in **all** these places:
- Spoken conversation (stand-ups, workshops, interviews).
- Written documentation.
- Code — class names, method names, variables, tests.
- Event / message names.
- User stories and acceptance criteria.

If a word appears in one of those places but not another, fix it: either rename
the code to match the conversation, or rename the conversation to match the
domain. (Almost always the *conversation* wins — domain language beats
technical convenience.)

### Scope
Ubiquitous Language is **per Bounded Context**, not global.

"Customer" in Sales BC and "Customer" in Support BC are **not the same word**
— they're homonyms. Within each BC, each term has exactly one meaning.

Across BCs, explicit translation (ACL) maps terms.

### It evolves
The language will change as understanding deepens. Good teams refactor their
language as deliberately as they refactor code. A rename in the domain is a
load-bearing event, not a cosmetic one.

### Who owns it
Everyone — but practically, someone has to curate the glossary so it stays
consistent. In this agent, that's the `glossary-curator` subagent.

## Useful quotes

> "The code is the ultimate expression of the ubiquitous language."

> "Any terminology used differently by different people will cause
> difficulties in communication."

## How this file should be cited

When a skill or agent needs a short justification for why the project needs a
strict glossary:

> Per `kb/canonical/fowler-ubiquitous-language.md`, the project's Ubiquitous
> Language must be used consistently across conversation, docs, and code —
> and must be maintained per Bounded Context, not globally.

## Interrogator pushbacks powered by this

- Synonyms in glossary for the same concept → **not allowed**. Pick one term,
  deprecate the other.
- Homonyms across BCs (same word, different meaning) → **allowed** but
  **explicitly flagged** in the glossary.
- Code uses a name not in the glossary → add the glossary entry, or rename the
  code.
- Stakeholder uses a new term → do not silently translate. Capture the new
  term in the glossary or negotiate a replacement.
- Technical jargon ("DTO", "handler") leaking into domain conversation → push
  back. The domain speaks in business terms.

## Practical glossary format (used by `projects/<slug>/4-glossary.md`)

```
## <Term>

**Bounded Context:** <which BC — or "global" if unambiguous>
**Definition:** <one sentence in business language>
**Examples:** <2–3 concrete examples>
**Synonyms to avoid:** <terms we are NOT using>
**Homonyms elsewhere:** <if this term means something different in another BC,
name it>
**First recorded:** <date>
```

## Related references

- Original bliki: https://martinfowler.com/bliki/UbiquitousLanguage.html
- Evans' coverage: `kb/canonical/evans-ddd-reference.md § Ubiquitous Language`
- Fowler on Bounded Context: `kb/canonical/fowler-bounded-context.md` — the
  complement; language is *always per* BC.
