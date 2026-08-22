# commit-message command

Generate a Conventional Commits message for **staged changes** and print it in a fenced code block. Does not run `git commit` or any other write git command.

## Install

Not covered by the skills CLI — install separately from the [source skill](source.md).

Copy [`commands/commit-message.md`](../commands/commit-message.md) to your agent's commands directory:

| Agent | Path |
| ----- | ---- |
| Cursor | `~/.cursor/commands/` |
| Claude Code | `~/.claude/commands/` |
| Others | your agent's commands directory |

## Usage

Run `/commit-message` when you have staged changes.

If nothing is staged, the command reports that and stops.

Output is a single conventional commit message (subject line, optional body) in a fenced code block for you to review and commit yourself.
