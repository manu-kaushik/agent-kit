---
name: source
description: Ensure SOURCE.md exists as the persistent project record; create or update AGENTS.md and/or CLAUDE.md to point to it. Use when the user runs /source or sets up project context. SOURCE carries project truth across all chats. Optional scope is agents or claude; omit scope for all guidance files.
disable-model-invocation: true
---

# Source

SOURCE.md is the **persistent project record** — stack, architecture, decisions, deferrals, focus. Chats are temporary; SOURCE.md is not. Any agent in any chat reads it first and updates it when project truth changes.

| File        | Role | Scope |
| ----------- | ---- | ----- |
| `SOURCE.md` | **Required** — whole project on disk | always |
| `AGENTS.md` | Agent workflow; enforces SOURCE-first behavior | default, `agents` |
| `CLAUDE.md` | Claude guidance; imports SOURCE via `@SOURCE.md` | default, `claude` |

## Scope

**SOURCE.md is always included.**

| Invocation | Action |
| ---------- | ------ |
| `/source` | SOURCE.md + AGENTS.md + CLAUDE.md |
| `/source agents` | SOURCE.md + AGENTS.md |
| `/source claude` | SOURCE.md + CLAUDE.md |

## Templates

All templates live in [templates/](templates/).

## Workflow

1. **Confirm target directory** — workspace root unless the user specifies another path.

2. **Determine scope** — no argument → all guidance files; `agents` or `claude` → that file only. SOURCE.md always runs.

3. **Resolve filenames** — prefer `SOURCE.md`, `AGENTS.md`, `CLAUDE.md`. Treat lowercase variants as the same file.

4. **SOURCE.md** (always)
   - **Missing** → create from [templates/SOURCE.md](templates/SOURCE.md).
   - **Exists** → never erase or replace content. Prepend [templates/SOURCE.prepend.md](templates/SOURCE.prepend.md) if marker `source:` is absent. Append template sections not already present.

5. **Bootstrap from repository** — fill empty table cells and placeholders only:
   - `README.md`, manifests, `Makefile`, CI configs, directory layout, scripts
   - Do not invent facts; use `<!-- TODO: confirm -->` when uncertain
   - Do not overwrite non-empty values

6. **Merge durable project knowledge** — from the current conversation and work context, append to SOURCE.md where missing:
   - **Decisions** — new choices and rationale (append; do not remove existing)
   - **Deferred** — planned work not yet done
   - **Architecture** — design facts not yet recorded
   - **Current focus** — rewrite only this section when focus clearly shifted
   - Skip ephemeral chat noise; record only facts that belong in the project record

7. **AGENTS.md** (when scope is default or `agents`) — create or prepend pointer if missing; preserve all existing content.

8. **CLAUDE.md** (when scope is default or `claude`) — create or prepend pointer if missing; preserve all existing content.

9. **Separation** — project facts live in SOURCE.md only. Guidance files enforce read/update behavior; do not duplicate facts.

10. **Idempotent** — skip prepending when marker is present.

11. **Summarize** — briefly (≤5 lines): scope, one-line status per project file, SOURCE.md gaps still needing input. No template field lists or content dumps.

## Ongoing use

`/source` ensures structure and catches up SOURCE.md from the repo and current context. During normal work in any chat, agents update SOURCE.md when project truth changes — without waiting for `/source`.

Agent behavior changes → edit AGENTS.md or CLAUDE.md. Project facts change → edit SOURCE.md.
