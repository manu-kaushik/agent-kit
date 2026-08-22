---
name: source
description: Maintain SOURCE.md as the persistent project record. Use for /source init (bootstrap), /source check (read-only health report), or /source refresh (reconcile from repo). Optional flags --agents or --claude; bare /source runs init.
disable-model-invocation: true
---

# Source

SOURCE.md is the **persistent project record** — stack, architecture, decisions, deferrals, focus. Chats are temporary; SOURCE.md is not. Any agent in any chat reads it first and updates it when project truth changes.

| File        | Role |
| ----------- | ---- |
| `SOURCE.md` | **Required** — whole project on disk |
| `AGENTS.md` | Agent workflow; enforces SOURCE-first behavior |
| `CLAUDE.md` | Claude Code guidance; imports SOURCE via `@SOURCE.md` |

## Guidance files and tools

| Scope | Guidance file | Tools |
| ----- | ------------- | ----- |
| `agents` | `AGENTS.md` | Cursor, Codex, Amp, Cline, Windsurf, Antigravity, and similar |
| `claude` | `CLAUDE.md` | Claude Code only |

## Commands

**SOURCE.md is always in scope.** Guidance file scope is controlled by flags or detection (init and refresh only).

| Invocation | Action |
| ---------- | ------ |
| `/source init` | Bootstrap — create or update SOURCE + detected guidance file |
| `/source init --agents` | Bootstrap — SOURCE + AGENTS.md |
| `/source init --claude` | Bootstrap — SOURCE + CLAUDE.md |
| `/source check` | Read-only health report — no file writes |
| `/source check --agents` | Check SOURCE + AGENTS Boundaries only |
| `/source check --claude` | Check SOURCE + CLAUDE Boundaries only |
| `/source refresh` | Reconcile SOURCE (and guidance sync per scope) from repo evidence |
| `/source refresh --agents` | Refresh SOURCE + sync AGENTS.md |
| `/source refresh --claude` | Refresh SOURCE + sync CLAUDE.md |
| `/source` | Same as `/source init` |

No `all` flag. For both guidance files, run `init --agents` and `init --claude` separately.

### Detect running tool (init without flags)

Use system/context signals from the current session — not install path, not chat history:

| Running in | Guidance scope |
| ---------- | -------------- |
| Claude Code | `claude` |
| Cursor, Codex, Amp, Cline, Windsurf, Antigravity, or similar | `agents` |
| Unknown | `agents` |

## Templates

All templates live in [templates/](templates/).

## Workflow

1. **Confirm target directory** — workspace root unless the user specifies another path.

2. **Determine command** — `init` (including bare `/source`), `check`, or `refresh`.

3. **Determine guidance scope** (init and refresh only)
   - `--agents` or `--claude` → use that scope.
   - No flag on init → detect running tool (see table above).
   - No flag on refresh → detect running tool.
   - Check without flags → report SOURCE only unless `--agents` / `--claude` limits guidance checks.

4. **Resolve filenames** — prefer `SOURCE.md`, `AGENTS.md`, `CLAUDE.md`. Treat lowercase variants as the same file.

### init — bootstrap

5. **SOURCE.md**
   - **Missing** → create from [templates/SOURCE.md](templates/SOURCE.md) only. Do not prepend anything else.
   - **Exists** → never erase project content from `## Overview` downward.
     - **Marker** — if `source:` marker is absent, add `<!-- source: source-of-truth marker -->` as the first line.
     - **Normalize header** — between `# Source` and `## Overview`, keep exactly one intro paragraph (from template). Remove duplicate source-of-truth intros from older runs.
     - **Sections** — append template sections not already present; do not overwrite non-empty values.

6. **Bootstrap from repository** — fill empty table cells and placeholders only:
   - Read `README.md`, dependency manifests, `Makefile`, CI configs, top-level layout, key scripts
   - When conversation context is empty or minimal, treat this step as primary
   - Do not invent facts; use `<!-- TODO: confirm -->` when uncertain
   - Do not overwrite non-empty values

7. **Merge durable project knowledge** — from the current conversation, append where missing: Decisions, Deferred, Architecture; rewrite Current focus only when focus clearly shifted. Skip if no conversation context.

8. **AGENTS.md** (when guidance scope is `agents`) — create from template if missing; prepend pointer if marker absent; sync default Boundaries block from template (marker rules below).

9. **CLAUDE.md** (when guidance scope is `claude`) — create from template if missing; prepend pointer if marker absent; sync default Boundaries block from template.

10. **Summarize** init — scope, one-line status per file touched, gaps needing input (≤5 lines). If the other guidance file does not exist, note: run `/source init --[other]` if this repo is also used with that tool.

### check — read-only health report

**Do not create, edit, or delete any file.**

5. **Read** SOURCE.md, `README.md`, dependency manifests, CI configs, top-level directory layout, and guidance files in scope.

6. **Report** to the user in three groups:
   - **OK** — sections that match the repo or need no action
   - **Stale** — SOURCE claims X, repo shows Y (cite paths)
   - **Missing** — template sections absent, marker missing, duplicate header intros, Boundaries block behind template, empty critical cells with repo data available

7. **End with:** `Run /source refresh to reconcile.` Do not fix anything.

### refresh — reconcile

If SOURCE.md is missing → tell the user to run `/source init` first; stop.

5. **SOURCE.md** — apply init steps 5–7, plus conservative reconciliation:
   - Update factual fields (Stack, Commands, Repository layout) only when SOURCE is empty, contains `<!-- TODO: confirm -->`, or repo evidence clearly contradicts SOURCE
   - When ambiguous, add `<!-- stale: … -->` rather than overwriting
   - Normalize header (one intro between `# Source` and `## Overview`)
   - Remove completed **Deferred** items only when repo or conversation evidence clearly shows completion

6. **AGENTS.md / CLAUDE.md** (per guidance scope) — sync default Boundaries from template:
   - Replace block between `<!-- source: boundaries-default -->` and `<!-- /source: boundaries-default -->` (markers inclusive)
   - If markers absent → insert template block after `## Boundaries`; preserve `<!-- Project-specific additions below -->` and content after it
   - Never remove project-specific additions

7. **Summarize** refresh — what changed, what remains stale or needs manual input (≤5 lines). Suggest `/source check` to verify.

### Boundaries sync (init and refresh)

Extract the marked block from [templates/AGENTS.md](templates/AGENTS.md) or [templates/CLAUDE.md](templates/CLAUDE.md). Never overwrite content after `<!-- Project-specific additions below -->`.

### Separation

Project facts live in SOURCE.md only. Guidance files enforce read/update behavior; do not duplicate facts.

## Ongoing use

During normal work, agents update SOURCE.md when project truth changes — without waiting for `/source`. Agent behavior → edit AGENTS.md or CLAUDE.md. Project facts → edit SOURCE.md.
