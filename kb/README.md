# DDD Knowledge Base (`kb/`)

The agent's long-term memory. Skills and subagents **must cite paths from this
directory** when invoking DDD principles — this is the anti-hallucination
anchor.

## How it's organized

```
kb/
├── canonical/       Foundational definitions from the DDD canon (Evans, Vernon, Fowler)
├── ceremonies/      How to run the ideation ceremonies (Event Storming, Domain Storytelling, Example Mapping, BC Canvas)
├── patterns/        Strategic patterns and decision frameworks (Context Mapping, Cynefin, Core Domain Chart, Aggregate Design)
├── voices/          Modern practitioner pattern language (Verraes, Tune, Khononov, Baas-Schwegler)
└── anti-patterns/   What the interrogator should push back on (anemic model, 1-BC-per-subdomain myth, premature tactical design)
```

## Frontmatter conventions

Every file has YAML frontmatter so skills/agents can filter by tier and tag:

```yaml
---
title: <short descriptive title>
source: <upstream URL or "native" if written here>
author: <author name or "ddd-agent native">
license: <CC BY 4.0 | MIT | CC BY-ND 3.0 | fair-use summary | public domain>
tier: 1 | 2 | 3 | 4       # 1 = foundational canon, 4 = specialised/case-study
tags: [bounded-context, aggregate, event-storming, ...]
ingested: YYYY-MM-DD
vendored_from: <upstream repo or author, if copied>
---
```

## Reading order for a new project

If you want to understand what the agent "knows", read in this order:

1. `canonical/evans-ddd-reference.md` — foundational definitions
2. `canonical/fowler-bounded-context.md` — what a BC actually is
3. `canonical/fowler-ubiquitous-language.md`
4. `patterns/cynefin-when-to-ddd.md` — *when is DDD even appropriate?*
5. `ceremonies/event-storming.md`
6. `ceremonies/bounded-context-canvas.md`
7. `patterns/context-mapping-patterns.md`
8. `voices/verraes-bc-not-1-on-1.md` — the myth-buster
9. `anti-patterns/*` — what to push back on

## Citation format

When a skill or agent references KB material in output, use this format:

```
Per kb/ceremonies/event-storming.md, past-tense is non-negotiable...
```

This makes every claim traceable and falsifiable.
