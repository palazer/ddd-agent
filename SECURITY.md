# Security Policy

## Scope

This repository ships **prompts, agent definitions, and a curated knowledge
base** — there is no executable code at runtime, no network endpoints, no
persisted user data, and no privileged operations. The realistic security
surface is therefore narrower than a typical software project, and limited to:

- **Prompt-injection or jailbreak content** smuggled into a `kb/` entry, a
  skill, or an agent definition that could subvert another user's agent
  behaviour when they install this plugin.
- **Personal information leaking** through ceremony outputs (e.g. an example
  artifact accidentally committed under `projects/` rather than gitignored).
- **Supply-chain concerns** in the marketplace manifest (`.claude-plugin/`)
  if a malicious fork replaces it.

If you find something matching one of those categories, please report it
privately rather than opening a public issue.

## Reporting a vulnerability

**Preferred:** open a [private security advisory] on this repository via the
GitHub Security tab. GitHub keeps the advisory invisible to the public until
disclosure, which is the right channel for anything sensitive.

[private security advisory]: https://docs.github.com/en/code-security/security-advisories/working-with-repository-security-advisories/creating-a-repository-security-advisory

**Fallback:** if you can't use GitHub Security Advisories for some reason,
open a regular GitHub issue **without** any reproducer details and ask a
maintainer to contact you privately. We'll move the discussion off the public
issue tracker.

## What to expect

This is a small, personal-scale project — there is no formal security team
or SLA. In practice:

- Acknowledgement: best-effort within ~7 days.
- Triage and fix: depends on severity. Prompt-injection issues that affect
  installed users are top priority; cosmetic or theoretical concerns may be
  documented and deferred.
- Disclosure: we'll coordinate a public-disclosure timeline with the
  reporter once a fix is in place.

## Versions covered

The latest tagged release on `main` is the only "supported" version. Older
releases will not be patched.

## Out of scope

- Issues in upstream OSS projects vendored under `vendor/` (which is
  gitignored and not distributed) — please report those upstream.
- Issues in Claude Code itself, the Anthropic SDK, or the Claude API —
  please report those to Anthropic via their security channels.
- "The agent gave bad DDD advice" — that's a quality concern, not a
  security one. Open a regular issue using the bug or question template.
