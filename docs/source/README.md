# source skill

Create and maintain **SOURCE.md** — the persistent project record for a repo — plus agent guidance files that enforce reading and updating it.

## What SOURCE.md is

Stack, architecture, decisions, deferrals, focus — on disk, not in chat history. Agents read it first and update it when project facts change.

## Quick start

### Install

```bash
npx skills add manu-kaushik/agent-kit --list   # preview

npx skills add manu-kaushik/agent-kit --skill source
```

### Manual install

Copy [`skills/source/`](../../skills/source/) to your agent's skills directory.

Open a project and run `/source init`.

## Commands

```
/source <command> [--agents | --claude]
```

`SOURCE.md` is always in scope. Optional flags pick which guidance file to create or check; omit them and the skill detects the running tool (Cursor, Codex, etc. > `AGENTS.md`; Claude Code > `CLAUDE.md`).

### init

Bootstrap a project — creates or updates `SOURCE.md` and the guidance file for your tool.

- **Writes files** — yes
- **Flags** — `--agents` or `--claude` to target a specific guidance file; omit to detect from the running tool
- **When to use** — first setup in a repo, or when adding a guidance file for another tool (`/source init --claude`, etc.)

### check

Read-only health report — compares `SOURCE.md` (and guidance files in scope) against the repo and prints OK / stale / missing. **Does not create, edit, or delete any file.**

- **Writes files** — no
- **Flags** — `--agents` or `--claude` to include that guidance file in the report; omit for `SOURCE.md` only
- **When to use** — periodic audit; ends with a suggestion to run `/source refresh` if anything needs fixing

### refresh

Reconcile `SOURCE.md` from repo evidence and sync default Boundaries in guidance files.

- **Writes files** — yes
- **Flags** — same as `init`
- **When to use** — after `check` reports stale or missing items
- **Conservative** — updates empty or TODO fields and clear repo mismatches; does not blindly overwrite filled Decisions or Stack entries

## Files created/updated by source when run in a project

| File        | Role                                                                                     |
| ----------- | ---------------------------------------------------------------------------------------- |
| `SOURCE.md` | **Required** — your project context (the source of truth) living in your project on disk |
| `AGENTS.md` | Workflow for Cursor, Codex, and similar agents                                           |
| `CLAUDE.md` | Claude Code guidance; imports SOURCE via `@SOURCE.md`                                    |
