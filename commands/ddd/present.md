---
description: Generate or refresh the audience-graduated 4-tier prose companion (`0-presentation.md`) for a DDD project. Lets a third party engage at TL;DR / strategic / per-BC / appendix depths without walking the full ceremony evidence trail. Derivative-only — never originates a fact.
argument-hint: "--slug <project-slug> [--resync] [--tier 1|1+2|all|custom]"
---

# /ddd:present

Produce a single `projects/<slug>/0-presentation.md` artifact that
presents the model at four audience tiers — exec, architect, developer,
auditor — with reader-controlled depth.

The command is **interrogator-style**: it inspects the project's
upstream artifacts, recommends a supportable tier set with explicit
skip reasoning, and gates each tier expansion behind `AskUserQuestion`
rather than dumping all four tiers on the user.

Canonical curation: [`kb/patterns/model-presentation-tiers.md`](../../../kb/patterns/model-presentation-tiers.md).

## Why this exists

The artifacts produced by `/ddd:ideate` (`1-cynefin-framing.md` through
`7-context-map.md`) are **process records** — they preserve the order
of inquiry, the pushbacks, the abandoned hypotheses. A third party
reading them cold gets fragmented context: every artifact reads at one
density, with no audience cue and no executive surface.

`/ddd:present` produces a derivative cover sheet: TL;DR for execs,
strategic frame for architects, per-BC detail for developers, link-only
appendix for auditors. The artifacts themselves stay untouched —
sparring evidence is preserved.

## When to use — and when not to

| Situation | Use |
|---|---|
| You finished `/ddd:ideate` and want to share the model with someone outside the ceremony. | `/ddd:present --slug <slug>` |
| You ran `/ddd:revise` for a cascading change and the existing presentation may be stale. | `/ddd:present --slug <slug> --resync` |
| You only want the executive surface for a quick share. | `/ddd:present --slug <slug> --tier 1` |
| You want diagrams generated as part of the presentation. | Out of scope. Run `/ddd:diagrams` separately — the presentation cites whatever already exists in `0-diagrams/`. |
| You want a hand-crafted README outside the ceremony chain. | Refuse — that's the orphan-summary anti-pattern (`kb/patterns/model-presentation-tiers.md § Anti-patterns`). |

## Arguments

- `--slug <project-slug>` — **required**. Which project to present.
  Single-project — never crosses projects (CLAUDE.md §2).
- `--resync` — **optional**. Walk the artifact-impact matrix
  backwards to find tiers whose upstream sources changed since the
  presentation's `Last updated:` footer; propose per-tier
  regeneration. Mirrors `/ddd:diagrams --resync`.
- `--tier 1|1+2|all|custom` — **optional**. Pre-select tier scope,
  bypassing the interactive recommendation gate.
  - `1` — Tier 1 (exec) + Tier 4 (appendix). Tier 4 is always
    included — appendix is link-only and cheap.
  - `1+2` — Tier 1 + Tier 2 (strategic frame) + Tier 4.
  - `all` — Tier 1 + Tier 2 + Tier 3 (per-BC detail) + Tier 4.
    Tier 2 is still skipped if `7-context-map.md` is missing.
  - `custom` — open interview from scratch with no presets.

## Behaviour

1. **Discovery** — silently scan `projects/<slug>/` to detect which
   upstream artifacts exist; count BCs, canvases, ADRs, diagrams.
   Compute which tiers are supportable and which are skipped (with
   reason).
2. **Recommend** — present the supportable tier set + skip rationale
   + length-budget estimate. Ask the user to pick tier scope via
   `AskUserQuestion`.
3. **Synthesise** — for each chosen tier, derive prose by citing
   upstream artifacts verbatim. Glossary terms used as-is
   (CLAUDE.md §2). Pushbacks and open questions surfaced verbatim,
   not polished away.
4. **Hand-off** — emit `projects/<slug>/0-presentation.md` with the
   full tier stack, a reading guide, and a `## Back-links → Prose
   sources` block for resync.
5. **Suggest** — offer follow-ups: run `/ddd:diagrams` if any
   `(no T<n.n> diagram present)` placeholders appear; schedule a
   `--resync` after the next `/ddd:revise` cascading run.

For the full operational spec — phase signals, length budgets, per-tier
synthesis rules, resync drift mapping, interrogator behaviours — see
[`skills/model-presentation/SKILL.md`](../../skills/model-presentation/SKILL.md).

## Preconditions

- `projects/<slug>/` exists.
- At minimum `1-cynefin-framing.md` and `5-bc-candidates.md` exist —
  required for Tier 1. Without them the command aborts with a pointer
  to run `/ddd:ideate` or the relevant ceremony first.
- `4-glossary.md` exists (may be seed only — every tier cites it).

If preconditions fail, the command emits a clear message naming the
missing artifact, e.g.:

> Tier 1 requires `5-bc-candidates.md` but none found in
> `projects/wallet-mvp/`. Run `/ddd:ideate` (or `/ddd:bc-canvas`
> directly) first, then re-run `/ddd:present`.

## Hard rules carried over from CLAUDE.md

- **Plöd-L3 ceiling** (§1) — per-tier user gate in `--resync` mode.
- **Ubiquitous Language sacred** (§2) — every term verbatim from
  `4-glossary.md`. Synonyms route to `glossary-curator` rather than
  being introduced.
- **No tactical code** (§3) — presentation describes the model, not
  implementations. Mentioning aggregate names is fine; describing how
  they're implemented is not.
- **Cite or don't claim** (§4) — every section references either an
  upstream `projects/<slug>/` artifact or a `kb/` reference. The
  emitted file's footer carries the canonical provenance line.
- **Revisions enforced** (§6) — the presentation is a downstream
  consumer in the impact matrix
  (`kb/patterns/artifact-impact-matrix.md § Presentation as a
  downstream type`). Cascading changes upstream trigger a
  `--resync` candidacy.

## Outputs

```
projects/<slug>/
└── 0-presentation.md     ← single-file artifact; reader-controlled depth
```

The presentation is intentionally **one file** — the audience-graduation
job is solved by tier sections inside one document, not by a directory
of fragments. A reader picks their depth by jumping to the right tier.

## Skill invoked

`model-presentation` — see
[`skills/model-presentation/SKILL.md`](../../skills/model-presentation/SKILL.md).

## Related commands

- `/ddd:ideate` — produces the prose artifacts this command presents.
- `/ddd:revise` — after a cascading change, run `/ddd:present --resync`
  to refresh the affected tiers.
- `/ddd:diagrams` — independent visual peer. The presentation cites
  whatever diagrams already exist in `0-diagrams/` but never invokes
  the diagrams skill.
- `/ddd:bc-canvas` — adds canvases that unlock Tier 3 per-BC
  subsections.

## References

- [`kb/patterns/model-presentation-tiers.md`](../../../kb/patterns/model-presentation-tiers.md) —
  4-tier curation; audience labels; skip rules; resync semantics.
- [`kb/patterns/artifact-impact-matrix.md`](../../../kb/patterns/artifact-impact-matrix.md) §
  Presentation as a downstream type — cascade hook spec.
- [`kb/patterns/ddd-diagram-catalog.md`](../../../kb/patterns/ddd-diagram-catalog.md) —
  visual peer; the presentation pairs prose tiers with diagram tiers.
