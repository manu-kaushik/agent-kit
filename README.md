# agent-kit

Personal, reusable kit of AI-agent skills, commands, and optional rules — portable across Cursor, Claude Code, Codex, and other agents supported by the skills CLI.

## Skills

### source

Persistent project memory for any repo; creates and maintains `SOURCE.md` plus agent guidance files. [Full reference →](docs/source/)

```bash
npx skills add manu-kaushik/agent-kit --skill source
```

Run `/source init` in a project to bootstrap. Manual install: copy [`skills/source/`](skills/source/) to your agent's skills directory.

## Commands

### commit-message

Conventional commit message for staged changes; prints the message only, never commits. [Full reference →](docs/commit-message/)

Copy [`commands/commit-message.md`](commands/commit-message.md) to your agent's commands directory (e.g. `~/.cursor/commands/`).

## Rules

### Engineering standards

Optional principles, code quality, testing, safety, and communication rules. Copy the topics a project needs. [Full reference →](docs/engineering-standards/)

Cursor: copy files from [`rules/engineering/`](rules/engineering/) into the project's `.cursor/rules/`. Other agents: paste the bullets into `AGENTS.md` after the project-specific marker. `npx skills add` does not install these.

## License

[MIT](LICENSE)
