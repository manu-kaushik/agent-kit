<!-- source: source-of-truth marker -->

# Source

Persistent project record for this repository — not chat history, not summaries. Any agent in any chat reads this file first and updates it when project facts change.

## Overview

A personal, reusable kit of AI-agent tooling — skills, commands, and optional rules — portable across Cursor, Claude Code, and other agents that consume the same files. Built for daily personal use first; public because it may help others.

## Current focus

v2 source skill shipped (`init`, `check`, `refresh`). Deferred work: subagents, install scripts, commit-message as skill (see Open decisions).

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
├── docs/
│   ├── source/
│   │   └── README.md
│   ├── ignore/
│   │   └── README.md
│   ├── commit-message/
│   │   └── README.md
│   └── engineering-standards/
│       └── README.md
├── LICENSE
├── SOURCE.md
├── AGENTS.md
├── skills/
│   ├── source/
│   │   ├── SKILL.md
│   │   └── templates/
│   └── ignore/
│       └── SKILL.md
├── commands/
│   └── commit-message.md
└── rules/
    └── engineering/
        ├── principles.mdc
        ├── code-quality.mdc
        ├── testing.mdc
        ├── safety.mdc
        └── communication.mdc
```

`skills/` at root is the skills CLI container directory. Only files named `SKILL.md` are discoverable; `commands/*.md`, `rules/**/*.mdc`, and future `agents/*.md` stay invisible to the CLI.

## Commands

| Task    | Command |
| ------- | ------- |
| Install | `npx skills add manu-kaushik/agent-kit --skill source` or `--skill ignore` |
| Dev     | — |
| Test    | `npx skills add manu-kaushik/agent-kit --list` (expect `source` and `ignore`) |
| Lint    | — |
| Build   | — |

`commit-message` is a manual copy to your agent's commands directory (e.g. `~/.cursor/commands/`, `~/.claude/commands/`).

Engineering standards are a manual copy of the chosen files in `rules/engineering/` into a project's `.cursor/rules/`, or of their bullets into `AGENTS.md` after the project-specific marker. They are not applied in this repo.

## Configuration

No env vars or secrets. Skills CLI: `npx skills add manu-kaushik/agent-kit --skill source` or `--skill ignore` (no `-g` flag — not supported). Manual paths include `~/.cursor/skills/`, `~/.claude/skills/`, and equivalent paths for Codex and other supported agents.

This repo uses **AGENTS.md** only — no root `CLAUDE.md`.

## Architecture

Three commands; `SOURCE.md` is always in scope. Flags `--agents` or `--claude`:

| Command | Writes? | Purpose |
| ------- | ------- | ------- |
| `init` | Yes | Bootstrap SOURCE + one guidance file (detect tool if no flag); copy project facts from an existing AGENTS.md or CLAUDE.md into empty SOURCE sections |
| `check` | No | Read-only OK / stale / missing / shape / duplicated report |
| `refresh` | Yes | Conservative reconcile from repo; rewrite a long Current focus to one paragraph; remove a completed Deferred item or a resolved Open decisions item; sync Boundaries |

For both guidance files, run `init --agents` and `init --claude` separately.

Two skills, one directory each under `skills/`: `source` and `ignore`. Do not reintroduce an `agents/` + `claude/` split — two `SKILL.md` copies of the same name would publish as two skills.

## Conventions

- Conventional Commits
- Skill frontmatter includes `disable-model-invocation: true` when the skill must only run on explicit `/` invocation
- Relative links inside a skill stay relative to that skill directory
- A future subagent file uses exactly `name`, `description`, `model`, `readonly`, and `is_background`. There is no `tools:` field. `.cursor/agents/` wins over `.claude/agents/` and `.codex/agents/`.
- A future installer is a flat map of source directory names and needs both `install.sh` and `install.ps1`. The Claude plugin marketplace schema is unconfirmed.

## Constraints

- MIT license
- Ships the `source` and `ignore` skills, the `commit-message` command, and optional engineering rules — no subagents, installers, or plugin manifests
- Distribution: skills CLI / `npx skills add manu-kaushik/agent-kit --skill source` or `--skill ignore`
- One `SKILL.md` per skill name. Do not add a second copy of the same skill.

## External services

- GitHub — source of truth and install source for the skills CLI
- skills.sh / `npx skills` — discovery and install; search ranking is telemetry-driven and not a goal for this repo

## Decisions

- **Single `skills/` tree** — one copy of each skill at repo root so the skills CLI does not publish duplicate skill names.
- **Source skill commands** — `/source init` (bootstrap), `/source check` (read-only report), `/source refresh` (conservative reconcile). Flags `--agents` / `--claude` optional.
- **Boundaries sync on init/refresh** — default Do/Do not blocks live between `<!-- source: boundaries-default -->` markers; preserved project-specific additions after `<!-- Project-specific additions below -->`.
- **Single SOURCE.md intro** — marker plus one paragraph under `# Source`; normalize on init/refresh.
- **Reading SOURCE.md** — AGENTS.md template: Current focus + Overview first; other sections on demand.
- **v1 distribution via skills CLI only** — no custom installer or Claude plugin marketplace yet; commands remain a documented manual copy.
- **OSS baseline** — MIT + README only; skip CONTRIBUTING, CODE_OF_CONDUCT, issue templates, and CI until an external contributor opens an issue.
- **No versioning for v1** — track `main` only; users re-run `npx skills update`. Tag only if a skill behavior change would break existing dependents.
- **GitHub topics** — `agent-skills`, `claude-code`, `cursor`, `ai-agents`. Description: "A personal, reusable kit of AI-agent tooling — skills and commands, portable across tools."
- **Skills CLI install** — `npx skills add manu-kaushik/agent-kit --skill source` only; `-g` is not supported (verified).
- **agent-kit repo guidance** — `AGENTS.md` only; no root `CLAUDE.md` (Cursor workflow for this repo).
- **SOURCE section shape** — Current focus is one paragraph (refresh rewrites it when longer). Deferred and Open decisions are one line each; refresh removes a line only when the work is done or the question is resolved, and a resolved question is appended to Decisions first. Decisions stay append-only; supersede with a new bullet that names the old one. `check` reports Shape separately. No second progress file and no character cap. The section map lives in the source skill and the AGENTS.md template. Init copies that template only when AGENTS.md is missing; refresh syncs the Boundaries block and leaves the rest of an installed AGENTS.md alone.
- **Facts copied from existing guidance** — init and refresh read an existing AGENTS.md and CLAUDE.md and copy project facts into empty SOURCE sections. Workflow, boundaries, and the guidance files stay, including the copied paragraphs. The summary names what was copied so those paragraphs can be removed by hand. When the two files disagree, the section stays `<!-- TODO: confirm -->`.
- **Engineering standards are optional** — five rules in `rules/engineering/` (principles, code quality, testing, safety, communication), each `alwaysApply: false`. Copy the ones a project needs. They are not part of `/source init` and not installed by the skills CLI. Boundaries stay in the source skill. This repo does not install them into its own `.cursor/rules/`.
- **Ignore skill** — `/ignore` writes a marked `.cursorignore` block for secrets and stack build or vendor folders. It does not repeat Cursor defaults (`.env*`, `node_modules/`, lockfiles). `!**/.env.example`, `!**/.env.sample`, and `!**/.env.template` stay visible. It does not edit `.gitignore`, and `/source init` does not run it. Supersedes the single-skill limit in the older v1 constraint.
- **Ignore skill covers other tools** — the same secrets and stack folders are written to Claude Code (`Read` deny rules in `.claude/settings.json`), Codex (a permission-profile deny table, skipped when `sandbox_mode` is set), Windsurf (`.codeiumignore`), Cline (`.clineignore`), Gemini CLI (`.geminiignore`), Antigravity (`.antigravityignore`), and `.aiexclude`. `.claudeignore` is not created. Amp is skipped because it has no ignore file. Supersedes the Cursor-only ignore decision above.
- **Skills CLI install includes ignore** — `npx skills add manu-kaushik/agent-kit --skill source` or `--skill ignore`. `-g` is still not supported. Supersedes the source-only install decision above.
- **One Project context** — init adds the source-of-truth marker to an existing Project context section. It prepends the pointer file only when that section is missing, so a templated guidance file does not get a second one.

## Deferred

- Subagents: `code-reviewer`, `docs-keeper`, and `board-triage` only. The file shape is under Conventions.
- Distribution: `install.sh`, `install.ps1`, and a Claude Code plugin marketplace manifest, after commands and subagents exist.
- Additional skills: capture them when they resurface. Do not invent a list.

## Open decisions

- `commit-message`: keep it a command, or convert it to a skill with `disable-model-invocation: true` so the skills CLI can install it.
