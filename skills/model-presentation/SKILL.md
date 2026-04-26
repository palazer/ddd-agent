---
name: model-presentation
description: Generate the audience-graduated 4-tier prose companion (`0-presentation.md`) for a DDD project. Synthesises Tier-1 exec surface, Tier-2 strategic frame, Tier-3 per-context detail, and Tier-4 appendix from existing upstream artifacts. Derivative-only; never originates a fact. Drives `/ddd:present`.
argument-hint: --slug <project-slug> [--resync] [--tier 1|1+2|all|custom]
allowed-tools: Read, Write, Edit, Glob, Grep, Skill, Agent, AskUserQuestion
---

# Model Presentation (4-Tier Prose Companion)

Facilitator skill for `/ddd:present`. Produces a single
`projects/<slug>/0-presentation.md` artifact that lets a third party
engage with the project at their own depth — TL;DR for execs, strategic
frame for architects, per-BC detail for developers, link-only appendix
for auditors.

**Canonical reference:** `kb/patterns/model-presentation-tiers.md`.

## When to use — and when not to

| Situation | Use |
|---|---|
| You finished `/ddd:ideate` and need to share the model with someone outside the ceremony. | `/ddd:present` |
| You ran `/ddd:revise` for a `cascading` change and the existing presentation may be stale. | `/ddd:present --resync` |
| You only want to refresh one tier without touching the others. | `/ddd:present --tier custom` |
| You want the presentation to also generate diagrams. | Out of scope. Run `/ddd:diagrams` separately — the presentation cites whatever already exists in `0-diagrams/`. |
| You want to write a hand-crafted README outside the ceremony chain. | Refuse. A README that does not enter the impact matrix is the orphan-summary anti-pattern (`kb/patterns/model-presentation-tiers.md § Anti-patterns`). |

## Arguments

- `--slug <project-slug>` — **required**. Which project to present.
  Single-project skill — never crosses projects (CLAUDE.md §2).
- `--resync` — **optional**. Walk the artifact-impact matrix backwards
  to find tiers whose upstream sources changed since the
  presentation's `Last updated:` footer; propose per-tier
  regeneration. Mirrors `/ddd:diagrams --resync`.
- `--tier 1|1+2|all|custom` — **optional**. Pre-select tier scope,
  bypassing the interactive recommendation gate.
  - `1` — Tier 1 + Tier 4 (always include the appendix).
  - `1+2` — Tier 1, Tier 2, Tier 4.
  - `all` — Tiers 1, 2, 3, 4 (Tier 2 still skipped if upstream missing).
  - `custom` — open interview from scratch with no presets.

## The 5-phase flow

```
┌──────────────────────────────────────────────────────────────────┐
│ 1. Discovery   silently scan projects/<slug>/. Detect which      │
│                upstream artifacts exist; count BCs, canvases,    │
│                ADRs, diagrams. Compute which tiers are           │
│                supportable and which are skipped (with reason).  │
│                                            [no user gate]       │
│ 2. Recommend   present the supportable tier set + skip rationale │
│                + length-budget estimate. Ask the user to pick    │
│                tier scope.                                       │
│                                            [AskUserQuestion]    │
│ 3. Synthesise  for each chosen tier, derive the prose by citing  │
│                upstream artifacts verbatim. Glossary terms       │
│                used as-is (CLAUDE.md §2). No paraphrasing of     │
│                contested decisions.                              │
│                                            [no user gate]       │
│ 4. Hand-off    emit projects/<slug>/0-presentation.md with the   │
│                full tier stack and the canonical footer +        │
│                back-links block. Optionally update the           │
│                project's existing README convention if present.  │
│                                            [no user gate]       │
│ 5. Suggest     offer two follow-ups:                              │
│                - run `/ddd:diagrams` if T1.2 / T2.1 are missing  │
│                  (the presentation pointed at them)              │
│                - schedule a `/ddd:present --resync` after the    │
│                  next `/ddd:revise` cascading run                │
│                                            [AskUserQuestion]    │
└──────────────────────────────────────────────────────────────────┘
```

## Phase 1 — Discovery (signals scanned)

Scanned per the tier-skip rules in
`kb/patterns/model-presentation-tiers.md`:

| Signal | Source | Affects which tier |
|---|---|---|
| `1-cynefin-framing.md` exists | file presence | T1 q1–2, T2 Cynefin section |
| `2-event-storm.md` exists | file presence | T1 q3 (actors) |
| Distinct actor count | `2-event-storm.md` | T1 q3 fidelity |
| `5-bc-candidates.md` exists | file presence | T1 q4, T2 classification |
| BC count | `5-bc-candidates.md` | T1 q4, T3 subsection count |
| Canvases in `6-contexts/` | directory listing | T3 per-BC sections |
| `7-context-map.md` exists | file presence | T1 q5, T2 context-map narrative |
| ADRs in `7-adr/` | directory listing | T2 risk synthesis, T4 table |
| Revisions in `8-revisions/` | directory listing | T4 revision table |
| `0-diagrams/` populated | directory listing | T1/T2/T3 diagram pointers, T4 diagram table |
| `4-glossary.md` exists | file presence | mandatory — every tier cites verbatim |
| Existing `0-presentation.md` | file presence | resync candidate |

Discovery emits a structured signals YAML to the calling agent — no
file writes.

## Phase 2 — Recommendation (one AskUserQuestion)

The agent presents the supportable tier set with reasoning. Example
output:

> **Detected:**
> - `1-cynefin-framing.md`, `2-event-storm.md` (12 actors),
>   `5-bc-candidates.md` (5 BCs), `7-context-map.md` ✓
> - 4 canvases in `6-contexts/` (BC `Wallet` has no canvas yet)
> - 7 ADRs, 3 revisions, 5 diagrams in `0-diagrams/`
> - No existing `0-presentation.md`
>
> **Tier 1 — Executive surface** ✓
> Estimated 2½ pages. All sources present. Question 4 will list 5 BCs;
> question 3 will pull 12 actors (collapsed to top 6 by event-storm
> frequency).
>
> **Tier 2 — Strategic frame** ✓
> Estimated 4 pages. All required sources present. Risks/strengths
> synthesis will draw from canvas § 11 (12 open questions across the 4
> existing canvases) and the 7 ADRs.
>
> **Tier 3 — Per-context detail** ✓
> Estimated 4 pages (4 canvas-backed BCs × ½ – 1 pp). The `Wallet` BC
> will be listed as candidate-without-canvas, not invented.
>
> **Tier 4 — Appendix** ✓ (always)
> Estimated ½ page. Will cite all 7 ADRs, 3 revisions, 5 diagrams.
>
> Total estimate: ~11 pages of derivative prose. Proceed with all
> tiers?

Options (always exactly these four, in this order):
1. `Confirm all four tiers` — proceed with full stack.
2. `Tier 1 + Tier 4 only` — exec surface + appendix; skip the middle.
3. `Tier 1 + Tier 2 + Tier 4` — skip per-BC detail.
4. `Pick individually` — re-prompt with a multi-select listing each
   tier separately.

When a tier is **not supportable** (e.g., no `7-context-map.md` →
Tier 2 cannot be filled), the recommendation says so explicitly:

> **Tier 2 — Strategic frame** — **skipping** (no `7-context-map.md`
> found; the strategic-classification table alone without a
> context-map narrative is strictly worse than no Tier 2 — see
> `kb/patterns/model-presentation-tiers.md § Skip rules`).

## Phase 3 — Synthesise (one tier at a time)

For each chosen tier:

1. Read the canonical structure for that tier from
   `kb/patterns/model-presentation-tiers.md § Tier N`.
2. Read the upstream artifacts listed in that tier's "Required upstream
   artifacts" line.
3. Synthesise the tier's sections by:
   - **Citing glossary terms verbatim** (CLAUDE.md §2). Refuse to
     introduce a synonym. If a label seems missing, route to
     `glossary-curator` agent.
   - **Citing decisions and invariants verbatim** from canvases /
     ADRs. The presentation may shorten — never rewrite.
   - **Citing pushbacks and open questions verbatim** from canvas
     § 11. The presentation surfaces the contestation; it does not
     resolve it.
4. Honour the tier's length budget. If a section threatens to bust the
   budget, abridge — do not split into a new section.
5. Cross-reference the diagram catalog: where a tier section pairs with
   a diagram (per the table in
   `kb/patterns/model-presentation-tiers.md § Relationship to ddd-diagram-catalog`),
   include either the existing `0-diagrams/<NN>-*.md` pointer or a
   "no <ID> diagram present — run `/ddd:diagrams`" stub.

The skill never edits sibling artifacts during synthesis. Drift between
the presentation and a peer is surfaced at Phase 5, not fixed here.

## Phase 4 — Hand-off

After all chosen tiers are emitted:

- Emit `projects/<slug>/0-presentation.md` with this structure:

```markdown
# <Project name> — Model Presentation

**Date:** <YYYY-MM-DD>
**Slug:** <slug>
**Tiers included:** <list, e.g. "Tier 1, Tier 2, Tier 4">
**References:** kb/patterns/model-presentation-tiers.md, kb/patterns/ddd-diagram-catalog.md

---

## Reading guide

| If you are… | Read… |
|---|---|
| New to this project / non-DDD | Tier 1 (≤ 3 pp) |
| Architect or eng lead | Tier 1 + Tier 2 (~7 pp) |
| Working on a specific BC | Tier 1 + the relevant Tier 3 subsection (~4 pp) |
| Auditor or future maintainer | Tier 4 + cross-reference to `8-revisions/` |

---

## Tier 1 — Executive surface

### TL;DR

<5–8 sentences synthesised from upstream>

### The model in five questions

**1. What domain are we in?**
<paragraph from 1-cynefin-framing.md>

**2. What is genuinely complex here?**
<paragraph — Complex/Core classification rationale>

**3. Who are the actors?**
- <actor 1> — <one-line role from 2-event-storm.md>
- <actor 2> — ...

**4. What are the bounded contexts and how many are Core?**
1. **<BC 1 name>** — <one-line purpose from 5-bc-candidates.md or canvas § 2>
2. **<BC 2 name>** — ...

**5. How do the contexts relate?**
<paragraph from 7-context-map.md>

> Visual: see `0-diagrams/02-bounded-context-map.md` (T1.2)

---

## Tier 2 — Strategic frame

### Cynefin framing summary
<from 1-cynefin-framing.md>

### Strategic classification
| BC | Importance | Business model | Wardley | Life expectancy |
|---|---|---|---|---|
| <BC> | Core/Supporting/Generic | ... | ... | ... |

### Context map narrative
<paragraph per non-trivial edge>

> Pattern semantics: see `kb/patterns/context-mapping-patterns.md`.

### Top 3 risks
1. **<risk>** — surfaced in `6-contexts/<bc>.md § 11` (canvas open question)
2. ...

### Top 3 strengths
1. **<strength>** — verified by `6-contexts/<bc>.md § 10` (verification metric)
2. ...

---

## Tier 3 — Per-context detail

### <BC 1 name>
- **Purpose:** <verbatim from canvas § 2>
- **Strategic classification:** <one line from canvas § 3>
- **Domain roles:** <archetypes from canvas § 4, citing kb/patterns/bounded-context-archetypes.md>
- **Communication:** receives <X> from <Y>; emits <Z> to <W> (from canvas § 5–6)
- **Key invariants:** <verbatim from canvas § 8>
- **Open questions:** <verbatim from canvas § 11>
- **For depth:** `6-contexts/<bc-slug>.md` · diagram T1.3 in `0-diagrams/`

### <BC 2 name>
...

### <BC N — candidate, no canvas yet>
This BC is listed in `5-bc-candidates.md` but no canvas has been
filled. See the candidate description there. Run `/ddd:bc-canvas
<bc-name>` to flesh it out.

---

## Tier 4 — Appendix

### Source artifacts
- [`1-cynefin-framing.md`](./1-cynefin-framing.md) — domain Cynefin framing
- [`2-event-storm.md`](./2-event-storm.md) — Big-Picture Event Storm
- ... (one line per existing artifact)

### Architecture decisions
| ADR | Title | Status | Summary |
|---|---|---|---|
| 0001 | <title> | Accepted | <one line> |

### Revision history
| Revision | Kind | Summary |
|---|---|---|
| 0001 | cascading | <one line> |

### Diagrams
| ID | Diagram | Audience | Last updated |
|---|---|---|---|
| T1.1 | Domain Capability Map | exec | YYYY-MM-DD |

### Glossary
All terms used above are defined in [`4-glossary.md`](./4-glossary.md).

---

## Back-links → Prose sources

This presentation cites the following upstream artifacts. Any
`cascading` change in these triggers a `/ddd:present --resync`
candidacy per `kb/patterns/artifact-impact-matrix.md § Presentation
as a downstream type`.

- `1-cynefin-framing.md`
- `2-event-storm.md`
- `4-glossary.md`
- `5-bc-candidates.md`
- `6-contexts/<bc-slug>.md` (× N)
- `7-context-map.md`
- `7-adr/*` (× N)
- `8-revisions/*` (× N)
- `0-diagrams/<NN>-*.md` (× N)

---

*0-presentation · Generated by /ddd:present · Cited from kb/patterns/model-presentation-tiers.md · Last updated: <YYYY-MM-DD>*
```

The footer line follows the same provenance convention as
`0-diagrams/<NN>-*.md` — the resync mechanism greps it for the
`Last updated:` date.

## Phase 5 — Suggest follow-ups

Final `AskUserQuestion`:

> Presentation written to `projects/<slug>/0-presentation.md`.
> Suggested next steps:

Options:
1. `Run /ddd:diagrams` — if any `(no T<n.n> diagram present)`
   placeholders appear in the emitted file. Otherwise this option is
   not offered.
2. `Schedule resync` — recommend running `/ddd:present --resync`
   after the next `/ddd:revise` cascading run.
3. `Review and stop` — done. The user opens the file and reads.

## --resync mode behaviour

When invoked with `--resync`:

1. **Phase 1** scans `0-presentation.md` and parses its
   `## Back-links → Prose sources` block.
2. **Phase 1.5** reads the project's `4-glossary.md § Drift Sweep Log`
   and the most recent `8-revisions/NNNN-*.md`. Computes which prose
   files have changed since the presentation's `Last updated:` footer.
3. **Phase 2** presents the affected tier list with reasoning:
   > "Tier 1 question 4 (BC list) — `5-bc-candidates.md` was edited by
   > revision 0008 (added BC `Wallet`). Suggest regenerate Tier 1.
   > Tier 2 risks list — ADR-0019 added since last update; suggest
   > regenerate. Tier 3 — `Wallet` BC has no canvas yet; will be
   > listed as candidate-without-canvas. Tier 4 — auto-refresh
   > revision/ADR/diagram tables."
4. **Phases 3–5** run only over the confirmed-affected tiers. Untouched
   tiers are preserved verbatim.

`--resync` never invents tier content not previously present. If the
existing `0-presentation.md` was emitted with `--tier 1+2`, a resync
will not introduce Tier 3 — that requires a fresh `/ddd:present` run.

## Interrogator behaviours (enforced throughout)

- **Tier 1 must always be offered.** No project skips the executive
  surface — even sparse projects produce a Tier 1 placeholder naming
  what is missing rather than skipping.
- **No fact origination.** Every claim must trace to an upstream
  artifact. If the presentation surfaces a fact missing from upstream,
  the skill aborts that section and emits a "missing in upstream — run
  the relevant ceremony first" pointer.
- **Glossary terms verbatim** (CLAUDE.md §2). Unknown labels route to
  `glossary-curator` rather than being paraphrased.
- **Citations enforced** (CLAUDE.md §4). Every section header in the
  presentation references either an upstream artifact or a `kb/`
  reference.
- **Pushbacks preserved.** Open questions and contested decisions from
  canvases are surfaced verbatim. The presentation does not "polish"
  them away — that would defeat the sparring philosophy.
- **No tactical code.** The presentation never describes implementation
  (CLAUDE.md §3). Mentioning aggregate names is allowed (they're
  glossary terms); describing how an aggregate is implemented is not.
- **Per-tier user gate in --resync.** Plöd-L3 ceiling (CLAUDE.md §1)
  applies to the presentation as it does to canvases — the skill
  surfaces drift, the user confirms each tier patch.

## Outputs

```
projects/<slug>/
└── 0-presentation.md     ← this skill's only output
```

Single-file artifact. No directory of fragments — the audience-graduation
job is a job for one document with reader-controlled depth, not for a
docs site.

## Preconditions

- `projects/<slug>/` exists.
- At minimum `1-cynefin-framing.md` and `5-bc-candidates.md` exist —
  these are required for Tier 1 question 1, 2, 4. Without them, the
  skill aborts with a pointer to run `/ddd:ideate` or the relevant
  ceremony first.
- `4-glossary.md` exists (may be seed only — every tier cites it).
- `kb/patterns/model-presentation-tiers.md` is readable.

If preconditions fail, the skill aborts with a clear message naming
the missing artifact (e.g., "Tier 1 requires `5-bc-candidates.md`
but none found — run `/ddd:bc-canvas` or `/ddd:ideate` first").

## Skills/agents invoked

- `glossary-curator` agent — when a term is encountered in upstream
  that is not yet in `4-glossary.md`, route to `glossary-curator`
  rather than inventing a definition.

The presentation skill does **not** invoke `ddd-interrogator`,
`ddd-facilitator`, or `ddd-reviser`. It is a synthesis skill, not a
ceremony — there is nothing to spar over because every claim is
already in upstream.

## Related commands

- `/ddd:ideate` — produces the prose artifacts this skill presents.
- `/ddd:revise` — after a cascading change, run `/ddd:present --resync`
  to refresh the affected tiers.
- `/ddd:diagrams` — independent visual peer; the presentation cites
  whatever diagrams already exist in `0-diagrams/` but never invokes
  the diagrams skill.
- `/ddd:bc-canvas` — adds canvases that unlock Tier 3 subsections.

## References

- `kb/patterns/model-presentation-tiers.md` — canonical 4-tier
  curation; skip rules; resync semantics.
- `kb/patterns/ddd-diagram-catalog.md` — visual peer catalog; tier
  audience model.
- `kb/patterns/artifact-impact-matrix.md § Presentation as a
  downstream type` — cascade hook spec.
- `kb/canonical/fowler-ubiquitous-language.md` — verbatim-glossary
  basis.
- `CLAUDE.md §1` — Plöd-L3 ceiling; per-tier user gates.
- `CLAUDE.md §2` — Ubiquitous-Language sacredness; glossary terms
  verbatim.
- `CLAUDE.md §6` — Revision enforcement; presentation enters
  Renaming-Manifest cascade.
