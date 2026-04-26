# DDD Sparring Partner — Agent Instructions

> This file is intentionally minimal. The ETH Zurich AGENTbench study (March 2026) showed
> that verbose LLM-generated context files measurably hurt agent performance.
> Everything here is non-inferable from reading the repo.

## What this repo is

A personal **DDD (Domain-Driven Design) sparring partner** for the *ideation phase* of
new projects. It is not a code generator. It is not a tutorial. It is a set of
ceremonies, a curated knowledge base, and an interrogator agent that **pushes back**
rather than answers.

## Directory map (non-obvious parts only)

- `kb/` — curated DDD knowledge base. **Skills and agents MUST cite `kb/` paths** in
  their reasoning. This is the anti-hallucination anchor. Do not invent references.
- `research/` — source research documents. Read these for *why*, not *what*.
- `vendor/` — gitignored clones of upstream OSS assets. Reference only; never edited.
- `projects/<slug>/` — per-project working artifacts. One directory per idea you
  sparred about. **Do not mix artifacts across projects** — ubiquitous language is
  project-scoped. Layout:
  ```
  projects/<slug>/
  ├── input/                        # user-drops source docs (specs, research); read-only
  ├── 0-presentation.md             # /ddd:present — 4-tier prose cover sheet (derivative)
  ├── 0-diagrams/<NN>-*.md          # /ddd:diagrams — Mermaid visual companions (derivative)
  ├── 1-cynefin-framing.md
  ├── 2-event-storm.md
  ├── 3-domain-stories.md           # optional
  ├── 3b-example-maps/<story>.md    # optional, story-level rule clarification
  ├── 4-glossary.md                 # ubiquitous language; continuously updated
  ├── 5-bc-candidates.md
  ├── 6-contexts/<bc-slug>.md       # one BC canvas per major context
  ├── 7-context-map.md
  ├── 7-adr/NNNN-<title>.md         # MADR decision records
  └── 8-revisions/NNNN-<title>.md   # /ddd:revise audit trail (one per revision)
  ```
  Numeric prefixes reflect ceremony order; `0-` is reserved for **derivative
  cross-cutting outputs** (presentation, diagrams) that synthesise from many
  upstream sources. `3b` / `6` / `7` / `8` folder prefixes preserve reading
  order when expanded as directories.
- `skills/`, `agents/`, `commands/` — plugin components at the root, the
  canonical Claude Code plugin layout (auto-discovered at install time).

## Hard rules

1. **Plöd-L3 ceiling for core-domain work.** In Complex or core-domain contexts
   (per `kb/patterns/cynefin-when-to-ddd.md`), the agent sparrs — it does **not**
   decide. Always surface the decision back to the human.

2. **Ubiquitous Language is sacred.** Once a term is defined in a project's
   `projects/<slug>/4-glossary.md`, use it verbatim. Do not invent synonyms. If an
   apparent synonym shows up, flag it for the `glossary-curator` agent.

3. **No tactical code generation in v1.** This agent's scope ends at candidate
   bounded-context canvases + draft context map + ADR stubs. If asked to write
   aggregates, entities, or framework code, refuse and redirect to Phase 4 (not
   shipped).

4. **Cite or don't claim.** Any DDD principle invoked in output must cite a
   `kb/` file path. "Evans said X" without a path is forbidden.

5. **Anti-patterns in `kb/anti-patterns/` are non-negotiable pushbacks.** If a user
   proposal matches one, the interrogator must name the anti-pattern and explain
   why — not quietly accept it.

6. **Revisions are enforced, not remembered.** Any user-approved change to a
   DDD artifact that has downstream implications (a term rename, an event-name
   change, a BC boundary shift, a context-map edge change, a Business Decision
   addition, etc.) MUST be applied deterministically across all DDD-ideation
   artifacts in `projects/<slug>/`. Renaming is one change kind among several;
   the same protocol applies to all `cascading` changes recorded by
   `/ddd:revise`.
   Protocol:
   - The project's `4-glossary.md` carries a **Renaming Manifest** table:
     `{Kind, banned → canonical, scope, since-Decision, exception rules}`.
     The `Kind` column distinguishes `rename` / `redefine` /
     `boundary-shift` / `edge-change` / etc. Legacy rows without a `Kind`
     are treated as `rename`.
   - After any approved cascading change, run a drift-audit sweep: Grep each
     banned term across `projects/<slug>/*.md`, classify every hit as drift
     (rewrite) or legitimate (matches an exception rule), and record the
     result as an Audit Trail entry inside each touched file.
   - Full per-revision records live under `projects/<slug>/8-revisions/NNNN-*.md`
     (one file per `/ddd:revise` session). The impact analysis in each record
     cites `kb/patterns/artifact-impact-matrix.md`.
   - Legitimate exceptions are: lines marked `**Synonyms to avoid:**`, lines
     marked `Renamed from "X"` / `legacy`, references to external research
     documents or third-party API grammar (Fidel, Visa, EMVCo, OCPI).
   - Files explicitly scoped as **frozen** (external research) or **deferred**
     (legacy solution docs listed in plan.md §Files-to-Modify) are out of
     sweep scope — but that exclusion MUST be declared in the Renaming
     Manifest, not assumed.
   - **Phase 0 Shape may produce the triggering edit interactively.** When
     `/ddd:revise --shape` (or the auto-detected offer) runs, the agent
     co-edits the target artifact with the user live, before Triage. Those
     live-applied edits are subject to this same protocol: if the resulting
     change classifies as `cascading` in Phase 2, the sweep and the
     Renaming Manifest row are mandatory. Phase 0 itself never writes a
     Manifest row — Phase 5 does, after the full downstream walk confirms
     what actually moved.
   - "The agent will remember" is not an acceptable enforcement mechanism.

## Ubiquitous language slots (project-scoped; empty in root)

Per-project glossaries live in `projects/<slug>/4-glossary.md`. Root-level glossary
is empty on purpose — this agent is domain-agnostic.

## Commands

- `/ddd:ideate "<description>"` — full orchestrated ideation loop (forward
  pipeline; writes artifacts 1–7 from scratch).
- `/ddd:revise "<artifact-path-or-change-description>" --slug <slug> [--shape]` —
  reactive peer of `/ddd:ideate`. Classifies a change (cosmetic / structural
  / cascading), walks the artifact-impact matrix, and propagates the change
  across impacted artifacts with per-patch user gates. Writes a numbered
  record to `projects/<slug>/8-revisions/NNNN-*.md`. The optional `--shape`
  flag (or auto-detection on unchanged files / vague prose) enters Phase 0
  Shape — an ideation-style co-revision dialog that surveys the target,
  drafts Before/After edits, and applies them live before the sweep.
- `/ddd:event-storm`, `/ddd:domain-storytell`, `/ddd:example-map`,
  `/ddd:bc-canvas`, `/ddd:glossary` — direct ceremony jumps.
- `/ddd:diagrams "--slug <slug>" [--resync] [--tier 1|1+2|all|custom]` —
  visual companion generator. Interview-driven; recommends a Tier-1
  default set with reasoning, offers Tier 2/3/4 expansions based on
  detected signals. Emits Mermaid `.md` sources to
  `projects/<slug>/0-diagrams/` plus a `render.sh` for SVG hand-off.
  `--resync` mode walks the artifact-impact matrix backwards to detect
  prose-source drift and propose per-diagram regeneration. See
  `kb/patterns/ddd-diagram-catalog.md` for the 4-tier curation.
- `/ddd:present "--slug <slug>" [--resync] [--tier 1|1+2|all|custom]` —
  prose companion generator. Produces the 4-tier audience-graduated
  `0-presentation.md` so a third party can engage at TL;DR (exec),
  strategic-frame (architect), per-BC (developer), or appendix (auditor)
  depth without walking the full ceremony evidence trail.
  Derivative-only — never originates a fact, every claim cites
  upstream verbatim. `--resync` walks the impact matrix backwards
  per-tier. See `kb/patterns/model-presentation-tiers.md` for the
  4-tier curation and pairing with `kb/patterns/ddd-diagram-catalog.md`.
