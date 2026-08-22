# source skill

Create and maintain **SOURCE.md** — the persistent project record for a repo — plus agent guidance files that enforce reading and updating it.

## Install

The skills CLI installs from GitHub and detects which agent you are using. Commands are installed separately — see [commit-message.md](commit-message.md).

| Scope | Command |
| ----- | ------- |
| Every project | `npx skills add manu-kaushik/agent-kit --skill source -g` |
| Current project only | `npx skills add manu-kaushik/agent-kit --skill source` |

Preview available skills:

```bash
npx skills add manu-kaushik/agent-kit --list
```

**Manual install:** copy [`skills/source/`](../skills/source/) to your agent's skills directory (e.g. `~/.cursor/skills/source/`, `~/.claude/skills/source/`).

## What SOURCE.md is

Stack, architecture, decisions, deferrals, focus — on disk, not in chat history. Any agent in any chat reads SOURCE.md first and updates it when project facts change.

## Files written in a project

| File | Role |
| ---- | ---- |
| `SOURCE.md` | **Required** — whole project on disk |
| `AGENTS.md` | Workflow for Cursor, Codex, Amp, Cline, and similar agents |
| `CLAUDE.md` | Claude Code guidance; imports SOURCE via `@SOURCE.md` |

## Usage

`SOURCE.md` is always included. Which guidance files are written depends on the invocation:

| Invocation | Writes |
| ---------- | ------ |
| `/source` | `SOURCE.md` + guidance file for the **running tool** |
| `/source agents` | `SOURCE.md`, `AGENTS.md` |
| `/source claude` | `SOURCE.md`, `CLAUDE.md` |
| `/source all` | `SOURCE.md`, `AGENTS.md`, `CLAUDE.md` |

Bare `/source` detects the running tool (Cursor, Codex, etc. → `AGENTS.md`; Claude Code → `CLAUDE.md`). Explicit invocations override detection.

Re-runs are safe: bootstrap from the repo (even in an empty chat), merge durable knowledge from the current conversation, sync default Boundaries from the skill template, and never erase existing project content.

## Quick start

```bash
npx skills add manu-kaushik/agent-kit --skill source -g
```

Open a project and run `/source`.
