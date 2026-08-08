# Source

Persistent project record. This file is the single source of truth for the whole project — not chat history, not summaries. Any agent in any chat reads and updates this file. Keep it accurate.

## Overview

A personal, reusable kit of AI-agent tooling — skills, commands, and later subagents — portable across Cursor, Claude Code, and other agents that consume the same files. Built for daily personal use first; public because it may help others.

## Current focus

Shipped v1 (`source` skill, `commit-message` command). Recent change: bare `/source` detects the running tool and writes the matching guidance file; use `/source all` for both.

## Stack

| Layer      | Choice | Notes |
| ---------- | ------ | ----- |
| Language   | Markdown | Skills and commands are markdown; no application runtime |
| Framework  | — | — |
| Database   | — | — |
| Hosting    | GitHub | Public repo; skills CLI clones from default branch |

## Repository layout

```
agent-kit/
├── README.md
├── LICENSE
├── SOURCE.md
├── AGENTS.md
├── CLAUDE.md
├── skills/
│   └── source/
│       ├── SKILL.md
│       └── templates/
└── commands/
    └── commit-message.md
```

`skills/` at root is the skills CLI container directory. Only files named `SKILL.md` are discoverable; `commands/*.md` (and future `agents/*.md`) stay invisible to the CLI.

## Commands

| Task    | Command |
| ------- | ------- |
| Install | `npx skills add manu-kaushik/agent-kit --skill source` |
| Dev     | — |
| Test    | `npx skills add manu-kaushik/agent-kit --list` (expect exactly one skill: `source`) |
| Lint    | — |
| Build   | — |

`commit-message` is a manual copy to your agent's commands directory (e.g. `~/.cursor/commands/`, `~/.claude/commands/`).

## Configuration

No env vars or secrets. Skills CLI installs to the detected agent's skills directory. Manual paths include `~/.cursor/skills/`, `~/.claude/skills/`, and equivalent paths for Codex and other supported agents.

## Architecture

Two guidance files cover all agents; scope controls which get written:

| Scope | Guidance file | Tools |
| ----- | ------------- | ----- |
| `agents` | `AGENTS.md` | Cursor, Codex, Amp, Cline, Windsurf, Antigravity, etc. |
| `claude` | `CLAUDE.md` | Claude Code only |

Bare `/source` detects the running tool and picks `agents` or `claude`. Explicit `/source agents`, `/source claude`, or `/source all` overrides detection. Install location does not change skill behavior — the same `SKILL.md` runs everywhere.

One skill directory in-repo. Do not reintroduce an `agents/` + `claude/` split — two `SKILL.md` copies would publish as two skills with the same name.

Skill vs subagent vs command: a subagent gets a fresh context and only sees the parent prompt; use a skill when conversation context is required; use a command for short explicit procedures.

## Conventions

- Conventional Commits
- Skill frontmatter includes `disable-model-invocation: true` when the skill must only run on explicit `/` invocation
- Relative links inside a skill stay relative to that skill directory

## Constraints

- MIT license
- v1 ships only existing `source` skill and `commit-message` command — no new skills, subagents, installers, or plugin manifests
- Distribution for v1: skills CLI / `npx skills add manu-kaushik/agent-kit --skill source` only
- Exactly one `SKILL.md` in the tree

## External services

- GitHub — source of truth and install source for the skills CLI
- skills.sh / `npx skills` — discovery and install; search ranking is telemetry-driven and not a goal for this repo

## Decisions

- **Single `skills/` tree** — one copy of each skill at repo root so the skills CLI does not publish duplicate skill names.
- **Bare `/source` detects running tool** — default scope is `agents` (Cursor, Codex, etc.) or `claude` (Claude Code); `/source all` writes both guidance files. Explicit scope overrides detection.
- **Scope ≠ install location** — same skill file everywhere; detection uses runtime context, not install path.
- **v1 distribution via skills CLI only** — no custom installer or Claude plugin marketplace yet; commands remain a documented manual copy.
- **OSS baseline** — MIT + README only; skip CONTRIBUTING, CODE_OF_CONDUCT, issue templates, and CI until an external contributor opens an issue.
- **No versioning for v1** — track `main` only; users re-run `npx skills update`. Tag only if a skill behavior change would break existing dependents.
- **GitHub topics** — `agent-skills`, `claude-code`, `cursor`, `ai-agents`. Description: "A personal, reusable kit of AI-agent tooling — skills and commands, portable across tools."

## Deferred

**Subagents** — start with two or three focused ones, not a dozen generic helpers:

- `code-reviewer` — `readonly: true`, returns file/line, severity, and a concrete fix
- `docs-keeper` — keeps README and SOURCE.md accurate against the codebase
- `board-triage` — issue and PR triage via `gh`

Cursor subagent frontmatter is exactly five fields. There is no `tools:` field — that is Claude Code syntax and Cursor ignores it:

```yaml
---
name: code-reviewer
description: Job description the parent agent reads to decide when to delegate.
model: inherit
readonly: true
is_background: false
---
```

Cursor reads `.cursor/agents/`, `.claude/agents/`, and `.codex/agents/`, with `.cursor/` winning on name conflicts. A file using only `name`, `description`, and `model` is portable across both tools.

**Distribution**

- `install.sh` + `install.ps1` — needed once commands and subagents exist, since the skills CLI does not carry them. Source directory names already mirror destination directory names, so the installer is a flat mapping. `curl.exe` ships on Windows 10 1803+, but `| sh` does not work in cmd or PowerShell, so both scripts are required.
- Claude Code plugin marketplace manifest — the only channel that installs skills, subagents, and commands as one unit. Verify the schema against current docs before writing it; believed to be `.claude-plugin/marketplace.json` plus a per-plugin `.claude-plugin/plugin.json`, unconfirmed.

**Additional skills** — several were identified during real work but not written down. Capture them here as they resurface rather than inventing a list now.

## Open decisions

**`commit-message`: command or skill?** As a command it is a manual copy for every user. As a skill with `disable-model-invocation: true` it behaves identically in Cursor's `/` menu *and* becomes installable via `npx skills add`, which would make v1 zero-manual-step. The tradeoff is that Claude Code would surface it as a model-invoked skill rather than a slash command. Recommend converting; keeping it as a command is the lower-risk default.

**Versioning.** None for v1 (recorded under Decisions). Revisit only if a breaking skill change needs a pin.

**OSS baseline.** MIT + README only for now (recorded under Decisions). Revisit when an external contributor appears.
