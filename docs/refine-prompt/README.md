# refine-prompt command

Turn a short request into a **scoped implementation brief** — a one-line goal plus constraint bullets — and print it in a fenced code block. Reads the repo to ground the constraints; does not edit files or start the work.

## Install

Not covered by the skills CLI — install separately from the [source skill](../source/).

Copy [`commands/refine-prompt.md`](../../commands/refine-prompt.md) to your agent's commands directory:

| Agent | Path |
| ----- | ---- |
| Cursor | `~/.cursor/commands/` |
| Claude Code | `~/.claude/commands/` |
| Others | your agent's commands directory |

## Usage

Run `/refine-prompt <request>` in the project the work is for.

The command reads `SOURCE.md` (or `AGENTS.md` / `CLAUDE.md` when there is no `SOURCE.md`) and searches the code related to the request. It calls a mechanism "existing" only when it found one; otherwise the bullet is written as a requirement.

If an ambiguity would change the scope, up to 3 short questions follow the code block. Paste the brief into a new prompt, edit it first if needed.

## Example

Input:

```
/refine-prompt Add payment retry functionality.
```

Output:

```
Implement retry handling for transient payment-provider failures.
- Retry only failures classified as transient.
- Do not retry declined payments or invalid requests.
- Prevent duplicate charges using the existing idempotency mechanism.
- Use bounded retries and backoff.
- Preserve existing payment API contracts.
- Add unit and integration tests.
- Ensure retry attempts are observable.
- Do not modify unrelated payment flows.
```
