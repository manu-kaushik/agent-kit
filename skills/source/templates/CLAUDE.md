# Claude

Instructions for Claude Code in this repository.

## Project context

@SOURCE.md

Read SOURCE.md first — in every chat, before inferring from code or conversation. SOURCE.md is the persistent project record; chat history is not project memory.

When you learn or change durable project facts (stack, architecture, decisions, deferrals, focus), update SOURCE.md. Append to Decisions and Deferred; rewrite Current focus when it shifts. Do not duplicate project facts in chat or in this file.

Prefer SOURCE.md over this file or codebase inference when they conflict.

## Agent behavior

- Stay aligned with [AGENTS.md](AGENTS.md) when present for workflow and code-change expectations.
- Prefer editing existing files over creating new ones unless the task requires it.
- Ask before destructive git operations or credential-related changes.

## Claude-specific

<!-- Optional: slash commands, skills, MCP servers, tool permissions -->

| Item     | Location            |
| -------- | ------------------- |
| Commands | `.claude/commands/` |
| Skills   | `.claude/skills/`   |

## Boundaries

See AGENTS.md when present. Do not run `git commit` or `git push` unless explicitly asked. Add Claude-only overrides below if needed.
