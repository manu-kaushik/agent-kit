# Engineering standards

Optional rules for engineering practice. Copy the topics a project needs. `/source init` does not install them, and `npx skills add` does not carry them.

Git writes, builds, Docker builds, and migrations stay in the source skill's Boundaries block. These rules do not repeat those limits.

Each file has `alwaysApply: false`. Cursor pulls it when the task matches the description. Set `alwaysApply: true` on a copy when that project should always include that topic.

| File | Use when |
| ---- | -------- |
| [`principles.mdc`](../../rules/engineering/principles.mdc) | Changing code or choosing an approach |
| [`code-quality.mdc`](../../rules/engineering/code-quality.mdc) | Writing or editing code |
| [`testing.mdc`](../../rules/engineering/testing.mdc) | Adding behavior or changing tests |
| [`safety.mdc`](../../rules/engineering/safety.mdc) | Secrets, deletion, or destructive database work |
| [`communication.mdc`](../../rules/engineering/communication.mdc) | Finishing a task |

Skipping a file leaves that topic out.

## Cursor

Copy the chosen files into the project's `.cursor/rules/`.

```text
your-project/.cursor/rules/principles.mdc
your-project/.cursor/rules/testing.mdc
```

## Other agents

Paste the heading and bullets into `AGENTS.md` after `<!-- Project-specific additions below -->`. Omit the YAML frontmatter. `/source refresh` replaces the Boundaries block and leaves that region in place.

```markdown
## Testing

- Write tests for new functionality.
- Update existing tests when behavior changes.
- Run relevant tests after implementation.
- Never claim tests passed without executing them.
```

This repo does not install these rules into its own `.cursor/rules/`.
