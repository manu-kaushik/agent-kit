# agent-kit

Personal, reusable kit of AI-agent skills and commands — portable across Cursor, Claude Code, Codex, and other agents supported by the skills CLI.

## Skills

### source

Persistent project memory for any repo; creates and maintains `SOURCE.md` plus agent guidance files. [Full reference →](docs/source.md)

```bash
npx skills add manu-kaushik/agent-kit --skill source -g
```

Run `/source init` in a project to bootstrap. Manual install: copy [`skills/source/`](skills/source/) to your agent's skills directory.

## Commands

### commit-message

Conventional commit message for staged changes; prints the message only, never commits. [Full reference →](docs/commit-message.md)

Copy [`commands/commit-message.md`](commands/commit-message.md) to your agent's commands directory (e.g. `~/.cursor/commands/`).

## License

[MIT](LICENSE)
