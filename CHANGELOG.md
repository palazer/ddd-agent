# Changelog

All notable changes to ddd-agent are documented here. Format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/); the project
follows [Semantic Versioning](https://semver.org/) loosely (skill bundle
rather than a library).

## [Unreleased]

### Added — Prose presentation companion
- `/ddd:present` slash command — produces the 4-tier audience-graduated
  prose companion `projects/<slug>/0-presentation.md`. Solves the
  third-party-handoff problem: artifacts produced by `/ddd:ideate` are
  process records (preserve sparring trail) and don't read well cold.
  The presentation is a single-file derivative cover sheet with
  reader-controlled depth — TL;DR for execs, strategic frame for
  architects, per-BC detail for developers, link-only appendix for
  auditors. Derivative-only — never originates a fact, every claim
  cites upstream verbatim.
- `kb/patterns/model-presentation-tiers.md` — first-class canonical
  reference for the 4-tier curation: audience labels, length budgets,
  required upstream artifacts per tier, skip rules, resync semantics,
  pairing with `kb/patterns/ddd-diagram-catalog.md`.
- `skills/model-presentation/SKILL.md` — operational driver (5-phase
  flow: Discovery → Recommend → Synthesise → Hand-off → Suggest).
  Mirrors the `/ddd:diagrams` shape: interview-driven Tier-1 default
  with explicit skip reasoning, `--resync` mode for cascade-aware
  per-tier drift detection, `--tier 1|1+2|all|custom` pre-selection.
- `kb/patterns/artifact-impact-matrix.md` — extended to register
  `0-presentation.md` as a downstream consumer. Every prose row in
  the matrix now carries a `0-presentation.md (T<n>)` entry naming
  the affected tier IDs, parallel to the existing `0-diagrams/* (T<n.n>)`
  entries. New `## Presentation as a downstream type` section
  documents the cascade detection (`presentation-affected:` manifest
  row field), tier-aware drift table, resync mechanics, and
  independence from the diagrams skill.
- `CLAUDE.md` — directory map updated: `0-` prefix now documented as
  reserved for derivative cross-cutting outputs (presentation +
  diagrams). New `/ddd:present` command added to the Commands list.

### Notes on scope
- No new agent introduced. Synthesis is deterministic enough that
  the skill suffices; if iterative third-party-readability QA is
  needed later, a `presentation-reviewer` agent is the natural
  extension point.
- No version bump. This entry sits in [Unreleased] until the next
  tag; a MINOR bump to v1.1.0 is appropriate when released since this
  is an additive ceremony.

## [1.0.1] — 2026-04-26

Hotfix for v1.0.0 plugin manifest validation. No behavioural changes.

### Fixed
- `.claude-plugin/plugin.json` — removed `commands`, `agents`, `skills`
  fields that were rejected by Claude Code's plugin schema validator
  ("Invalid input"). Components are now auto-discovered.
- Component layout — moved `.claude/{agents,commands,skills}/` to
  `{agents,commands,skills}/` at plugin root, matching the canonical
  Claude Code plugin shape required for marketplace install.
- README, CONTRIBUTING.md, CLAUDE.md, and internal skill/agent
  cross-references updated for the new layout.

### Notes for v1.0.0 users
v1.0.0 published with a manifest layout that prevented `/plugin install`
from completing. v1.0.1 is the first install-correct release. No file
contents changed apart from path-reference updates; pulling latest
`main` and reinstalling is sufficient.

## [1.0.0] — 2026-04-26

Initial public release. Ideation + visual companions in one bundle.

### Added — Ideation
- `/ddd:ideate` orchestrator — 7-step DDD ideation loop (Cynefin framing
  → Big-Picture Event Storming → Domain Storytelling → Glossary → BC
  candidates → BC canvases → Context map + ADRs).
- `/ddd:revise` reactive peer — classifies a change (cosmetic /
  structural / cascading), walks the artifact-impact matrix, applies
  per-artifact patches with `AskUserQuestion` gates. `--shape` flag for
  ideation-style co-revision dialog.
- Direct ceremony commands: `/ddd:event-storm`, `/ddd:domain-storytell`,
  `/ddd:example-map`, `/ddd:bc-canvas`, `/ddd:glossary`.
- `kb/` curated knowledge base — canonical Evans/Vernon summaries,
  modern voices (Khononov, Verraes, Tune, Brandolini, Plöd,
  Baas-Schwegler), anti-patterns, ceremony references, patterns.
- `.claude/agents/{ddd-facilitator, ddd-reviser, ddd-interrogator,
  glossary-curator}.md` — orchestrator and specialist agents.

### Added — Visual companions
- `/ddd:diagrams` slash command — interview-driven Mermaid visual
  companion generator. Recommends a Tier-1 default set with explicit
  skip reasoning; offers Tier 2/3/4 expansions based on detected
  signals.
- `kb/patterns/ddd-diagram-catalog.md` — 4-tier curation of 14 Mermaid
  diagram IDs (T1.1 through T4.3) with locked Mermaid types and trigger
  conditions.
- `.claude/skills/diagram-catalog/` — operational driver (5-phase flow:
  Discovery → Recommend → Synthesise → Hand-off → Suggest) plus 14
  first-class `.md.tmpl` templates.
- Hybrid template pattern (markdown table + companion `flowchart LR`)
  for matrix-shaped diagrams: T2.2 actor-context, T3.3 visibility, T4.1
  coupling × volatility.
- T4.3 ships with an explicit "approximation only" banner — Mermaid
  cannot render true Hofer/Schwentner pictographs.
- `--resync` mode walks the artifact-impact matrix backwards to detect
  prose-source drift and propose per-diagram regeneration.
- `0-diagrams/render.sh` portable Mermaid-CLI hand-off for SVG
  rendering.

### Added — Distribution
- Plugin marketplace install path (`.claude-plugin/marketplace.json`,
  `.claude-plugin/plugin.json`).
- Dual-license: CC BY-SA 4.0 for `kb/` and `research/`; MIT for
  `.claude/` and code.
