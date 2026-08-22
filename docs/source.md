# source skill

Create and maintain **SOURCE.md** — the persistent project record for a repo — plus agent guidance files that enforce reading and updating it.

## Install

| Scope | Command |
| ----- | ------- |
| Every project | `npx skills add manu-kaushik/agent-kit --skill source -g` |
| Current project only | `npx skills add manu-kaushik/agent-kit --skill source` |

```bash
npx skills add manu-kaushik/agent-kit --list   # preview
```

**Manual install:** copy [`skills/source/`](../skills/source/) to your agent's skills directory.

## What SOURCE.md is

Stack, architecture, decisions, deferrals, focus — on disk, not in chat history. Agents read it first and update it when project facts change.

## Files in a project

| File | Role |
| ---- | ---- |
| `SOURCE.md` | **Required** — whole project on disk |
| `AGENTS.md` | Workflow for Cursor, Codex, Amp, Cline, and similar agents |
| `CLAUDE.md` | Claude Code guidance; imports SOURCE via `@SOURCE.md` |

## Commands

`SOURCE.md` is always included. Optional flags: `--agents` or `--claude` (no `all` — run twice if you need both guidance files).

| Command | Writes? | Purpose |
| ------- | ------- | ------- |
| `/source init` | Yes | Bootstrap SOURCE + guidance file for the **running tool** |
| `/source init --agents` | Yes | SOURCE + AGENTS.md |
| `/source init --claude` | Yes | SOURCE + CLAUDE.md |
| `/source check` | **No** | Report OK / stale / missing; suggests refresh |
| `/source refresh` | Yes | Reconcile SOURCE from repo; sync Boundaries |
| `/source` | Yes | Same as `init` |

### Typical flow

1. **`/source init`** — first setup (or bare `/source`)
2. **`/source check`** — periodic read-only audit
3. **`/source refresh`** — fix what check reported (conservative — won't overwrite filled facts without evidence)

Refresh updates empty or TODO fields and clear repo mismatches. It does not blindly replace your Decisions or filled Stack entries.

## Quick start

```bash
npx skills add manu-kaushik/agent-kit --skill source -g
```

Open a project and run `/source init`.
