# ignore skill

Write managed ignore rules for the tools that actually enforce them. Example env files stay visible. `.gitignore` is not edited.

## Install

```bash
npx skills add manu-kaushik/agent-kit --skill ignore
```

Manual install: copy [`skills/ignore/`](../../skills/ignore/) to your agent's skills directory.

Open a project and run `/ignore`. `/source init` does not run this skill.

## Files

| Tool | File | Notes |
| ---- | ---- | ----- |
| Cursor | `.cursorignore` | Does not repeat Cursor's default `.env*` or `node_modules/` list. Writes `!` lines so example env files stay visible. |
| Claude Code | `.claude/settings.json` | `Read` deny rules. `.claudeignore` has no effect and is not created. |
| Codex | `.codex/config.toml` | Deny rules on a permission profile. Skipped when `sandbox_mode` is set, or when the profile is `:danger-full-access`. |
| Windsurf | `.codeiumignore` | Gitignore syntax. |
| Cline | `.clineignore` | Gitignore syntax. Deprecated, and not a security boundary. |
| Gemini CLI | `.geminiignore` | Gitignore syntax. |
| Antigravity | `.antigravityignore` | Gitignore syntax. |
| Gemini Code Assist, Android Studio | `.aiexclude` | No `!` support. Lists concrete env names instead of `.env.*`, so `.env.example` is not excluded. |
| Amp | — | No ignore file. Amp's docs reject one. |

Each file keeps a marked block. A later `/ignore` replaces that block and leaves your other lines in place.

## What gets ignored

Secrets: `*.pem`, `*.key`, `id_rsa`, `credentials.json`, `secrets.json`.

Env files, except on Cursor (which already ignores `.env*`): `.env` and `.env.*`, with `.env.example`, `.env.sample`, and `.env.template` kept visible.

Stack folders, when a manifest is present: `node_modules/`, `.next/`, `.nuxt/`, `dist/`, `build/`, `coverage/`, `.turbo/`, `.venv/`, `venv/`, `__pycache__/`, `vendor/`, `target/`, `.dart_tool/`, `Pods/`, `bin/`, `obj/`.

Cursor skips anything on its own default list. A pattern already in `.gitignore`, or already outside the managed block, is skipped. The example-file exceptions are still written.
