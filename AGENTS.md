# Agents

Instructions for AI coding agents working in this repository.

## Project context

Read [SOURCE.md](SOURCE.md) first — in every chat, before inferring from code or conversation. SOURCE.md is the persistent project record; chat history is not project memory.

When you learn or change durable project facts (stack, architecture, decisions, deferrals, focus), update SOURCE.md. Append to Decisions and Deferred; rewrite Current focus when it shifts. Do not duplicate project facts in chat or in this file.

Prefer SOURCE.md over this file or codebase inference when they conflict.

## Workflow

- Confirm scope before large refactors or new dependencies.
- Match existing patterns in the codebase; read surrounding code before editing.
- Run relevant tests or lint after substantive changes when applicable.
- Do not commit, push, or open pull requests unless explicitly asked.

## Code changes

- Minimize diff scope; avoid unrelated edits.
- Preserve existing naming, types, and abstractions unless change is required.
- Add comments only for non-obvious logic.

## Boundaries

<!-- Project-specific do and do-not lists -->

**Do:**

- Update SOURCE.md when project truth changes

**Do not:**

- Run `git commit`, `git push`, or open pull requests unless explicitly asked
- Rely on conversation history for project facts — use SOURCE.md
- Remove or overwrite SOURCE.md entries without reason

## Verification

Before finishing, ensure changed behavior is covered by existing tests or manual steps documented in SOURCE.md.
