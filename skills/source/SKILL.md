---
name: source
description: Maintain SOURCE.md as the persistent project record. Use for /source init (bootstrap), /source check (read-only health report), or /source refresh (reconcile from repo). Optional flags --agents or --claude.
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

For both guidance files, run `init --agents` and `init --claude` separately.

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

2. **Determine command** — `init`, `check`, or `refresh`.

3. **Determine guidance scope** (init and refresh only)
   - `--agents` or `--claude` → use that scope.
   - No flag on init → detect running tool (see table above).
   - No flag on refresh → detect running tool.
   - Check without flags → report SOURCE only unless `--agents` / `--claude` limits guidance checks.

4. **Resolve filenames** — prefer `SOURCE.md`, `AGENTS.md`, `CLAUDE.md`. Treat lowercase variants as the same file.

### Section map

Write each fact into the section that owns it. Do not leave a session log in SOURCE.md.

| What you learned | Section | How to write it |
| ---------------- | ------- | --------------- |
| What we are doing now | **Current focus** | Replace with one short paragraph |
| Parked work | **Deferred** | One line; remove when that work is done |
| A durable choice | **Decisions** | Append one bullet: the choice and why |
| An unresolved question | **Open decisions** | One line; on resolve, append the choice to **Decisions** and delete it here |
| How the system works | **Architecture** | Update the relevant part |

When a task finishes, update the one section that owns the outcome.

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

7. **Copy facts from existing guidance files** — read `AGENTS.md` and `CLAUDE.md` when they already exist. Skip a file this run is about to create from the template.
   - Copy project facts into SOURCE sections that are empty, still the template comment, or only `<!-- TODO: confirm -->`.
   - Facts are overview, stack, commands, configuration, repository layout, architecture, conventions, constraints, external services, current focus, decisions, deferred work, and open questions. Write them with the section map's shape.
   - Leave workflow, boundaries, code-change rules, verification steps, and tool instructions in the guidance file.
   - Do not overwrite a filled SOURCE section. If the two guidance files disagree, leave `<!-- TODO: confirm -->` and name both files in the summary.
   - Do not delete either guidance file, and do not remove the copied paragraphs from them.

8. **Merge durable project knowledge** — from the current conversation, write each fact into the section that owns it (see Section map). Append **Decisions**. Rewrite **Current focus** only when focus clearly shifted. Skip if no conversation context. Do not append a session log.

9. **AGENTS.md** (when guidance scope is `agents`) — create from template if missing; prepend pointer if marker absent; sync default Boundaries block from template (marker rules below).

10. **CLAUDE.md** (when guidance scope is `claude`) — create from template if missing; prepend pointer if marker absent; sync default Boundaries block from template.

11. **Summarize** init — scope, one-line status per file touched, gaps needing input (≤5 lines). Name each SOURCE section filled from a guidance file and the file it came from. Those paragraphs stay in the guidance file for the user to remove. If the other guidance file does not exist, note: run `/source init --[other]` if this repo is also used with that tool.

### check — read-only health report

**Do not create, edit, or delete any file.**

5. **Read** SOURCE.md, `README.md`, dependency manifests, CI configs, top-level directory layout, guidance files in scope, and any existing `AGENTS.md` or `CLAUDE.md` for duplicated project facts. Boundaries stay limited to the guidance scope.

6. **Report** to the user in five groups:
   - **OK** — sections that match the repo or need no action
   - **Stale** — SOURCE claims X, repo shows Y (cite paths)
   - **Missing** — template sections absent (including **Open decisions**), marker missing, duplicate header intros, Boundaries block behind template, empty critical cells with repo data available
   - **Shape** — **Current focus** longer than one short paragraph; a **Deferred** item that is done or longer than one line; an **Open decisions** item longer than one line, or a resolved question still listed there; a **Decisions** bullet that is a task list or a multi-paragraph essay
   - **Duplicated** — an existing `AGENTS.md` or `CLAUDE.md` still states a project fact that SOURCE.md contains. Name the file and the SOURCE section. Do not delete it.

7. **End with:** `Run /source refresh to reconcile.` Do not fix anything. **Duplicated** paragraphs stay in the guidance file; the user removes those by hand.

### refresh — reconcile

If SOURCE.md is missing → tell the user to run `/source init` first; stop.

5. **SOURCE.md** — apply init steps 5–8, plus conservative reconciliation:
   - Update factual fields (Stack, Commands, Repository layout) only when SOURCE is empty, contains `<!-- TODO: confirm -->`, or repo evidence clearly contradicts SOURCE
   - When ambiguous, add `<!-- stale: … -->` rather than overwriting
   - Normalize header (one intro between `# Source` and `## Overview`)
   - When **Current focus** is longer than one short paragraph, rewrite it to one paragraph. Keep the active goal; drop the log. Do not invent a new focus.
   - Remove a **Deferred** item only when repo or conversation evidence clearly shows completion. Do not shorten a long **Deferred** item on refresh; `check` reports it under **Shape**.
   - Remove an **Open decisions** item only when it is clearly resolved: append the choice to **Decisions**, then delete the line. Do not shorten a long **Open decisions** item on refresh.
   - Leave **Decisions** in place. Do not delete or shorten them to save space. Supersede a choice by appending a new bullet that names the old one.
   - Do not trim SOURCE.md to a character or line cap.

6. **AGENTS.md / CLAUDE.md** (per guidance scope) — sync default Boundaries from template:
   - Replace block between `<!-- source: boundaries-default -->` and `<!-- /source: boundaries-default -->` (markers inclusive)
   - If markers absent → insert template block after `## Boundaries`; preserve `<!-- Project-specific additions below -->` and content after it
   - Never remove project-specific additions

7. **Summarize** refresh — what changed, which SOURCE sections were filled from a guidance file, and what remains stale or needs manual input (≤5 lines). Copied paragraphs stay in the guidance file for the user to remove. Suggest `/source check` to verify.

### Boundaries sync (init and refresh)

Extract the marked block from [templates/AGENTS.md](templates/AGENTS.md) or [templates/CLAUDE.md](templates/CLAUDE.md). Never overwrite content after `<!-- Project-specific additions below -->`.

### Separation

Project facts live in SOURCE.md only. Guidance files enforce read/update behavior. Init and refresh may copy facts from an existing guidance file into empty SOURCE sections; they leave that file and those paragraphs in place. After the copy, remove the fact paragraphs from the guidance file by hand. Do not copy workflow or boundaries into SOURCE.md.

## Ongoing use

During normal work, agents update SOURCE.md when project truth changes — without waiting for `/source init`. Follow the section map. Agent behavior → edit AGENTS.md or CLAUDE.md. Project facts → edit SOURCE.md.
