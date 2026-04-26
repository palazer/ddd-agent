# DDD Sparring Partner

A [Claude Code](https://docs.claude.com/en/docs/claude-code) skill bundle for the
**earliest ideation phase** of new projects — when the knowns are few, a proper
domain model has yet to emerge, and what you need most is someone to **argue
with** before you commit to a design.

This is not a code generator. It is not a tutorial. It is a curated knowledge
base, a set of DDD ceremonies, and an interrogator agent that **pushes back**
rather than answers.

[![License: dual (CC BY-SA 4.0 / MIT)](https://img.shields.io/badge/license-CC--BY--SA--4.0%20%2F%20MIT-informational)](LICENSE)
[![Status: v1](https://img.shields.io/badge/status-v1%20ideation--only-brightgreen)](#project-status)
[![Built for: Claude Code](https://img.shields.io/badge/built%20for-Claude%20Code-blueviolet)](https://docs.claude.com/en/docs/claude-code)

**v1 scope:** ideation + visual companions. Blank sheet → first bounded-context
candidates + draft context map + ADR stubs + Mermaid diagrams of the result
(capability map, context map, aggregate model, event ribbon, state machines,
plus qualifying Tier 2/3/4 IDs). Tactical code generation (aggregates,
entities, framework scaffolding) is deliberately out of scope — see
[Deferred to later versions](#deferred-to-later-versions).

---

## Philosophy

1. **Plöd L3** — in Complex / core-domain work, the agent **sparrs**, it does
   not decide. Every step checkpoints back to the human via
   `AskUserQuestion`. See `kb/patterns/cynefin-when-to-ddd.md`.
2. **Interrogator, not answerer** — the agent's job is to push back on
   premature tactical design, on "one bounded context per subdomain" thinking,
   and on anemic modelling. See `kb/anti-patterns/`.
3. **Cite or don't claim** — every DDD principle invoked must cite a path
   under `kb/`. The knowledge base is the anti-hallucination anchor. Output
   that says "Evans said X" without a `kb/` citation is treated as a defect.
4. **Ubiquitous Language is project-scoped** — `projects/<slug>/4-glossary.md`
   is sacred within that project; no cross-project leakage. The
   `glossary-curator` agent enforces term consistency.

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) installed (CLI,
  desktop app, or web — slash commands and skills work in all surfaces).
- macOS, Linux, or Windows-with-WSL. Tested primarily on macOS.
- No package installs, no API keys beyond what Claude Code itself needs, no
  build step. This repo ships prompts, agent definitions, and a knowledge base
  — nothing executable.

## Installation

This repo is published as a **Claude Code plugin marketplace**, so the
recommended install is one-line. Manual install options remain for users
who prefer them.

### Option A — Claude Code plugin marketplace (recommended)

From any Claude Code session in your project:

```
/plugin marketplace add palazer/ddd-agent
/plugin install ddd-agent@ddd-agent-marketplace
```

Then verify the slash commands registered:

```
/ddd:ideate "test domain — coffee subscription with skip weeks"
```

To pull upstream changes later:

```
/plugin marketplace update
```

> **Note for forkers**: replace `palazer/ddd-agent` with the GitHub
> owner/repo of your fork. The marketplace name (`ddd-agent-marketplace`)
> is fixed in `.claude-plugin/marketplace.json`.

### Option B — Clone and symlink (manual)

Keep the repo in one stable location, symlink the parts Claude Code needs into
each project where you want the DDD ceremonies available. This way `git pull`
upstream updates everything at once.

```bash
# 1. Clone somewhere stable (any path works; this is just an example):
git clone https://github.com/palazer/ddd-agent ~/tools/ddd-agent

# 2. From inside the project where you want to use the skills:
cd ~/code/your-project
mkdir -p .claude
ln -s ~/tools/ddd-agent/agents   .claude/agents
ln -s ~/tools/ddd-agent/skills   .claude/skills
ln -s ~/tools/ddd-agent/commands/ddd .claude/commands/ddd

# 3. Optional but recommended — the kb/ is referenced by skills and agents.
#    Symlink it into the project too, or keep the repo path stable so the
#    skills can resolve `kb/...` relative paths.
ln -s ~/tools/ddd-agent/kb .claude/kb
```

If the facilitator agent doesn't load the Cynefin framing skill in step 1,
re-check the symlinks resolve from the project root.

### Option C — Copy into a single project (manual)

If you only need the agent for one project and don't want symlink management:

```bash
git clone https://github.com/palazer/ddd-agent /tmp/ddd-agent
cp -r /tmp/ddd-agent/agents   ~/code/your-project/.claude/agents
cp -r /tmp/ddd-agent/skills   ~/code/your-project/.claude/skills
cp -r /tmp/ddd-agent/commands/ddd ~/code/your-project/.claude/commands/ddd
cp -r /tmp/ddd-agent/kb               ~/code/your-project/.claude/kb
```

Trade-off: you lose the ability to pull upstream updates without re-copying.

### Option D — Use this repo *as* your project root (also: the author dev loop)

If you want to keep all your ideation artifacts here, *or* you're a
contributor iterating on the plugin itself, you need one local symlink dance
because Claude Code only auto-discovers skills/agents/commands from
`.claude/<dir>/` in the current project — and v1.0 publishes the plugin
layout at the repo root (per the plugin schema), not under `.claude/`. The
fix is a four-line bootstrap, after which edits to `agents/`, `skills/`, or
`commands/` are picked up live with no install/cache step:

```bash
git clone https://github.com/palazer/ddd-agent ~/ddd-sparring
cd ~/ddd-sparring

# Bridge the plugin-root layout back into Claude Code's project loader.
# .claude/ is gitignored, so these stay local to your machine.
mkdir -p .claude/commands
ln -s ../agents          .claude/agents
ln -s ../skills          .claude/skills
ln -s ../../commands/ddd .claude/commands/ddd

claude
/ddd:ideate "your domain pitch goes here"
```

Each new ideation lands under `projects/<slug>/`, which is gitignored — so
your sparring artifacts stay local while the upstream skills remain pristine.
Authors: this is the equivalent of `pip install -e` for the plugin — every
edit to a file under `agents/`, `skills/`, or `commands/` is read live by
Claude Code. To dogfood the consumer-shape install in *other* projects,
follow Option A there.

## Usage

### Full ideation loop

```
/ddd:ideate "a multi-sided freelance marketplace with escrow and dispute resolution"
```

Walks a 7-step flow. Each step writes a numbered artifact under
`projects/<slug>/` so the reading order is self-evident:

1. **Cynefin framing** — is this even a DDD-shaped problem? → `1-cynefin-framing.md`
2. **Big-Picture Event Storming** — elicit events, actors, policies. → `2-event-storm.md`
3. **Domain Storytelling** (optional) — narrative flows for unclear areas. → `3-domain-stories.md`
4. **Ubiquitous Language** build — nouns, verbs, synonyms flagged. → `4-glossary.md`
5. **Candidate Bounded Contexts** — cluster events, apply Verraes' "BC ≠
   subdomain 1:1" lens. → `5-bc-candidates.md`
6. **Bounded Context Canvases** — one per candidate BC. → `6-contexts/<bc>.md`
7. **Context Mapping draft** — relationships + ADR stubs for pivotal decisions. → `7-context-map.md` + `7-adr/`

Between every step, the facilitator calls `AskUserQuestion` with three
options (`Proceed`, `Revise current step`, `Pause and save`). The loop only
advances on an explicit `Proceed`.

You can drop source documents into `projects/<slug>/input/` before invoking
the command — the facilitator reads them before Step 1 and threads them into
the Cynefin framing.

Once the loop completes, `/ddd:diagrams --slug <slug>` is the natural next
step — it produces Mermaid visual companions of the prose artifacts. See
[Visual companions](#visual-companions) below for the full catalog.

### Revising an existing project

Once artifacts exist, any subsequent edit may have downstream implications.
`/ddd:revise` is the reactive peer of `/ddd:ideate`:

```
/ddd:revise "rename Match-Decision-Log to Qualification-Decision-Log everywhere" --slug your-slug
```

It classifies the change (cosmetic / structural / cascading), walks the
artifact-impact matrix (`kb/patterns/artifact-impact-matrix.md`), and walks
you through per-artifact patches with `AskUserQuestion` gates. Produces a
numbered record at `projects/<slug>/8-revisions/NNNN-<title>.md`.

The optional `--shape` flag (or auto-detection on unchanged files / vague
prose) opens **Phase 0 Shape**: an ideation-style co-revision dialog that
surveys the target artifact, drafts Before/After edits, and applies them
live — *before* the cascade sweep. Useful when the change you have in mind
is fuzzy and needs interrogation before propagation.

### Direct ceremony jumps

If you know what you need:

- `/ddd:event-storm` — Big-Picture Event Storming only
- `/ddd:domain-storytell` — Domain Storytelling session
- `/ddd:example-map` — Example Mapping for a fuzzy rule
- `/ddd:bc-canvas` — fill/refine a Bounded Context Canvas
- `/ddd:glossary` — append/refine a project's ubiquitous language

### Visual companions

`/ddd:diagrams --slug <slug>` produces Mermaid sources that complement the
prose artifacts. Interview-driven: the skill scans the project, recommends
a Tier-1 default set with explicit skip reasoning, and gates Tier 2/3/4
expansion behind an `AskUserQuestion`. The catalog covers **14 IDs across
4 tiers**:

- **Tier 1 — universal essentials** (always offered): domain capability
  map, bounded context map, aggregate conceptual model, event ribbon,
  state machines.
- **Tier 2 — strategic frame** (offered if signals fire): core-domain
  heatmap, actor-context matrix, external-systems landscape (C4 Level 1).
- **Tier 3 — behavior & integration**: golden-path sequence per pivotal
  event chain, fallback decision tree, authorization / visibility matrix.
- **Tier 4 — reference & governance**: coupling × volatility matrix, ADR
  dependency graph, domain-stories pictograph (Mermaid approximation).

Outputs land in `projects/<slug>/0-diagrams/` as `.md` files with Mermaid
blocks. A portable `render.sh` is emitted alongside for the Mermaid-CLI
SVG hand-off.

Use `--resync` after a `/ddd:revise` cascade to detect prose-source drift
and propose per-diagram regeneration. See
[`kb/patterns/ddd-diagram-catalog.md`](kb/patterns/ddd-diagram-catalog.md)
for the full curation, trigger conditions, and Mermaid-type lookup.

## Repo layout

```
kb/              Curated DDD knowledge base (Tier-1 canon + voices + anti-patterns)
research/        Source research documents + OSS audit trail (read for *why*)
vendor/          (gitignored) Reference clones of upstream OSS assets
projects/<slug>/ (gitignored) Per-project working artifacts. Layout:
                 input/ (your source docs) + numbered ceremony outputs
                 (1-cynefin-framing.md … 7-context-map.md + 7-adr/
                 + 8-revisions/ for /ddd:revise audit records
                 + 0-diagrams/ for /ddd:diagrams Mermaid companions)
skills/          Plugin skills (auto-discovered at install time)
agents/          Specialist subagents (auto-discovered)
commands/        Slash commands under /ddd:* (auto-discovered)
.claude-plugin/  Plugin and marketplace manifests (drives /plugin install)
.github/         Issue templates and PR template
CLAUDE.md        Agent instructions — hard rules and directory map
SECURITY.md      Security disclosure policy
```

## Project status

**v1.0 is feature-complete for ideation + visual companions.** The 7-step
`/ddd:ideate` loop, the `/ddd:revise` cascade-walk, and the `/ddd:diagrams`
catalog (14 IDs across 4 tiers with first-class Mustache templates) are in
regular use; the knowledge base is curated and cited end-to-end.

This is a **personal sparring tool shared as-is**. There is no roadmap
commitment, no SLA, and no guarantee that the agent's pushbacks will match
your team's flavour of DDD. Use it as a starting point and fork freely.

PRs are welcome — the bar is *"matches the Plöd-L3 philosophy in
[`CLAUDE.md`](CLAUDE.md)"*. See [`CONTRIBUTING.md`](CONTRIBUTING.md) for what
that means in practice.

## Deferred to later versions

- Tactical code layer (jMolecules / ArchUnit / Spring Modulith)
- ContextMapper CML + MCP integration
- Structurizr DSL export and full C4 hierarchy (Levels 2–4); v1.0 ships a
  Mermaid C4 Level 1 system context (T2.3) but not deeper levels
- EventCatalog + AsyncAPI / OpenAPI contracts
- Kroki / server-side rendering pipeline; v1.0 ships a portable
  `render.sh` Mermaid-CLI hand-off, but Kroki integration for CI pipelines
  is a v2 alternative
- `/ddd:review` for assessing existing codebases
- Per-domain packs (glossaries + analogues for specific industries)

## Credits & Acknowledgements

This agent stands on the shoulders of several upstream OSS projects and the
DDD community's published work. The full audit trail with asset-by-asset
licensing decisions lives in
[`research/03-oss-audit.md`](research/03-oss-audit.md).

| Upstream source | License | Used for |
|---|---|---|
| [ddd-crew/bounded-context-canvas](https://github.com/ddd-crew/bounded-context-canvas) | CC BY 4.0 | `kb/ceremonies/bounded-context-canvas.md`, `kb/patterns/bounded-context-archetypes.md` |
| [ddd-crew/aggregate-design-canvas](https://github.com/ddd-crew/aggregate-design-canvas) | CC BY 4.0 | `kb/patterns/aggregate-design-canvas.md` |
| [ddd-crew/core-domain-charts](https://github.com/ddd-crew/core-domain-charts) | CC BY 4.0 | `kb/patterns/core-domain-chart.md`, `kb/patterns/core-domain-chart-examples.md` |
| [ddd-crew/ddd-starter-modelling-process](https://github.com/ddd-crew/ddd-starter-modelling-process) | CC BY 4.0 | `kb/patterns/ddd-starter-modelling-process.md` (orchestrator backbone) |
| [ddd-crew/ai-ddd-prompts-and-rules](https://github.com/ddd-crew/ai-ddd-prompts-and-rules) | reference only | concept inspiration for `agents/glossary-curator.md` (term-drift detection) |
| [melodic-software/claude-code-plugins](https://github.com/melodic-software/claude-code-plugins) | MIT | `skills/event-storming/`, `agents/{event-storming-persona,context-mapper,story-facilitator,adr-creator}.md` |
| Eric Evans — *DDD Reference* | CC BY 4.0 | condensed summaries in `kb/canonical/` |
| Vaughn Vernon — *Effective Aggregate Design* | CC BY-ND 3.0 | citation only — never reproduced |
| Anthropic — ["Building effective agents"](https://www.anthropic.com/engineering/building-effective-agents) | (essay) | evaluator-optimizer loop pattern in `agents/ddd-reviser.md` |
| Martin Fowler bliki, Mathias Verraes, Vlad Khononov, Nick Tune, Kenny Baas-Schwegler | various / fair-use citation | modern voices in `kb/voices/` |

Upstream contributors to the ddd-crew canvases include (non-exhaustive):
Kenny Baas, Kim Lindhard, Michael Plöd, Maxime Sanglan-Charlier, Mathew
McLoughlin, Marijn Huizendveld, Eduardo da Silva, Ciaran McNulty, Gien
Verschatse, James Morcom. See each upstream repo's `README` for the
authoritative attribution lists.

## License

This project is **dual-licensed** to honour the mixed nature of its
contents:

- **Knowledge artifacts** under `kb/` and `research/` — **CC BY-SA 4.0**.
  Matches the upstream ddd-crew lineage; share-alike on derivatives keeps
  the DDD knowledge commons open.
- **Agent code** under `agents/`, `commands/`, `skills/`, and `.claude-plugin/`
  — **MIT**. Maximally reusable in other Claude Code projects and downstream
  skill bundles.

Files outside these scopes default to **MIT**. Vendored upstream material
retains its original license; see each file's frontmatter and
[`research/03-oss-audit.md`](research/03-oss-audit.md) for the per-file
attribution ledger.

Full license texts: [`LICENSE`](LICENSE).
