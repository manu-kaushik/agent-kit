---
name: source
description: Ensure SOURCE.md exists as the persistent project record; create or update AGENTS.md and/or CLAUDE.md to point to it. Use when the user runs /source or sets up project context. SOURCE carries project truth across all chats. Bare /source detects the running tool; optional scope is agents, claude, or all.
disable-model-invocation: true
---

# Source

SOURCE.md is the **persistent project record** — stack, architecture, decisions, deferrals, focus. Chats are temporary; SOURCE.md is not. Any agent in any chat reads it first and updates it when project truth changes.

| File        | Role | Scope |
| ----------- | ---- | ----- |
| `SOURCE.md` | **Required** — whole project on disk | always |
| `AGENTS.md` | Agent workflow; enforces SOURCE-first behavior | `agents`, `all`; bare `/source` when not Claude Code |
| `CLAUDE.md` | Claude Code guidance; imports SOURCE via `@SOURCE.md` | `claude`, `all`; bare `/source` in Claude Code |

## Guidance files and tools

Scope names are not product names. Two guidance files cover all supported agents:

| Scope | Guidance file | Tools |
| ----- | ------------- | ----- |
| `agents` | `AGENTS.md` | Cursor, Codex, Amp, Cline, Windsurf, Antigravity, and other agents that read repo-root agent guidance |
| `claude` | `CLAUDE.md` | Claude Code only |

No separate template per tool — `AGENTS.md` is the portable file; Claude Code is the exception because of `@SOURCE.md` import syntax.

## Scope

**SOURCE.md is always included.**

| Invocation | Action |
| ---------- | ------ |
| `/source` | Detect running tool → `agents` or `claude`; write matching guidance file |
| `/source agents` | SOURCE.md + AGENTS.md |
| `/source claude` | SOURCE.md + CLAUDE.md |
| `/source all` | SOURCE.md + AGENTS.md + CLAUDE.md |

Explicit scope always overrides detection.

### Detect running tool (bare `/source` only)

Use system/context signals from the current session — not install path, not chat history:

| Running in | Scope |
| ---------- | ----- |
| Claude Code | `claude` |
| Cursor, Codex, Amp, Cline, Windsurf, Antigravity, or similar | `agents` |
| Unknown | `agents` |

## Templates

All templates live in [templates/](templates/).

## Workflow

1. **Confirm target directory** — workspace root unless the user specifies another path.

2. **Determine scope**
   - Explicit `agents`, `claude`, or `all` → use that scope.
   - No argument → detect running tool (see table above). SOURCE.md always runs.

3. **Resolve filenames** — prefer `SOURCE.md`, `AGENTS.md`, `CLAUDE.md`. Treat lowercase variants as the same file.

4. **SOURCE.md** (always)
   - **Missing** → create from [templates/SOURCE.md](templates/SOURCE.md).
   - **Exists** → never erase or replace content. Prepend [templates/SOURCE.prepend.md](templates/SOURCE.prepend.md) if marker `source:` is absent. Append template sections not already present.

5. **Bootstrap from repository** — fill empty table cells and placeholders only:
   - Read `README.md`, dependency manifests (`package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, etc.), `Makefile`, CI configs, top-level directory layout, and key scripts
   - When conversation context is empty or minimal, treat this step as primary — explore the repo before writing; do not skip bootstrap because the chat has no prior turns
   - Do not invent facts; use `<!-- TODO: confirm -->` when uncertain
   - Do not overwrite non-empty values

6. **Merge durable project knowledge** — from the current conversation and work context, append to SOURCE.md where missing:
   - **Decisions** — new choices and rationale (append; do not remove existing)
   - **Deferred** — planned work not yet done
   - **Architecture** — design facts not yet recorded
   - **Current focus** — rewrite only this section when focus clearly shifted
   - Skip ephemeral chat noise; record only facts that belong in the project record
   - If there is no conversation context, skip this step without blocking — repo bootstrap from step 5 is enough

7. **AGENTS.md** (when scope is `agents` or `all`)
   - **Missing** → create from [templates/AGENTS.md](templates/AGENTS.md).
   - **Exists** → prepend [templates/AGENTS.prepend.md](templates/AGENTS.prepend.md) if marker `source:` is absent; preserve all other existing content.
   - **Sync default Boundaries** from [templates/AGENTS.md](templates/AGENTS.md):
     - Extract the block between `<!-- source: boundaries-default -->` and `<!-- /source: boundaries-default -->` (markers inclusive).
     - Markers present → replace only that block with the template block.
     - Markers absent → insert the marked template block immediately after `## Boundaries`, removing any preceding unmarked default **Do** / **Do not** lists; keep `<!-- Project-specific additions below -->` and all content after it.
     - Never remove or overwrite project-specific additions (content after `<!-- Project-specific additions below -->`).

8. **CLAUDE.md** (when scope is `claude` or `all`)
   - **Missing** → create from [templates/CLAUDE.md](templates/CLAUDE.md).
   - **Exists** → prepend [templates/CLAUDE.prepend.md](templates/CLAUDE.prepend.md) if marker `source:` is absent; preserve all other existing content.
   - **Sync default Boundaries** from [templates/CLAUDE.md](templates/CLAUDE.md) — same marker rules as AGENTS.md step 7.

9. **Separation** — project facts live in SOURCE.md only. Guidance files enforce read/update behavior; do not duplicate facts.

10. **Idempotent** — skip prepending when marker is present. Sync default Boundaries from template on every run; do not modify guidance files outside the resolved scope.

11. **Summarize** — briefly (≤5 lines): detected or explicit scope, one-line status per project file touched, SOURCE.md gaps still needing input. No template field lists or content dumps.

    On first setup only (neither guidance file existed before this run) and scope is not `all`: add one line — e.g. "Run `/source all` if this repo is also used with [other tool]."

## Ongoing use

`/source` ensures structure and catches up SOURCE.md from the repo and current context. During normal work in any chat, agents update SOURCE.md when project truth changes — without waiting for `/source`.

Agent behavior changes → edit AGENTS.md or CLAUDE.md. Project facts change → edit SOURCE.md.
