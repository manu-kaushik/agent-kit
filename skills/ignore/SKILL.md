---
name: ignore
description: Write managed ignore rules for Cursor, Claude Code, Codex, Windsurf, Cline, Gemini, and Antigravity. Use for /ignore. Keeps env example files visible and does not edit .gitignore.
disable-model-invocation: true
---

# Ignore

`/ignore` writes the same secrets and stack folders for each tool that has a real ignore mechanism. It does not edit `.gitignore`. It does not run from `/source init`.

Amp has no ignore file. Do not invent one. Report that Amp was skipped.

## Shared candidates

Detect the stack from manifests at the repo root or one directory down. A pattern the stack does not justify is not a candidate. List each pattern once.

| Evidence | Patterns |
| -------- | -------- |
| `package.json` | `node_modules/`, `.next/`, `.nuxt/`, `dist/`, `build/`, `coverage/`, `.turbo/` |
| `pyproject.toml`, `requirements.txt`, or `Pipfile` | `.venv/`, `venv/`, `__pycache__/` |
| `composer.json` | `vendor/` |
| `Cargo.toml` | `target/` |
| `pom.xml`, `build.gradle`, or `build.gradle.kts` | `target/` |
| `pubspec.yaml` | `.dart_tool/`, `build/` |
| `Podfile` at the root or `ios/Podfile` | `Pods/` |
| `*.csproj`, `*.fsproj`, `*.vbproj`, or `*.sln` | `bin/`, `obj/` |

Secrets, for every tool that can express them:

```gitignore
*.pem
*.key
id_rsa
credentials.json
secrets.json
```

Env files, for every tool except Cursor:

```gitignore
.env
.env.*
!**/.env.example
!**/.env.sample
!**/.env.template
```

The `!` lines must come after `.env` and `.env.*`. Do not drop them because `.gitignore` already ignores `.env*`. Drop one only when that exact line already exists outside the managed block.

Cursor already ignores `.env*` by default. In `.cursorignore` only, omit `.env` and `.env.*`, and still write the three `!` lines so example files stay visible.

## Skip rules

Drop a candidate, and record the reason, when:

- The tool's own file already has that pattern outside the managed block. A trailing slash does not make it different.
- `.gitignore` already has that pattern. This does not apply to the three `!` env-example lines.
- The file is `.cursorignore` and the pattern is on Cursor's default list: `node_modules/`, `.next/`, `.nuxt/`, `.venv/`, `venv/`, `__pycache__/`, `.cache/`, `.gradle/`, lockfiles, or `.env*`.

Do not scan `node_modules/`, `vendor/`, `dist/`, `build/`, or `.git/`.

## Gitignore-style files

Use the shared candidates, then the skip rules. Write one pattern per line, no blank lines inside the block. Order: `.env`, `.env.*` (when this file gets them), the three `!` lines, secrets, then stack directories.

| File | Tool | Markers |
| ---- | ---- | ------- |
| `.cursorignore` | Cursor | `# cursor: ignore-managed` … `# /cursor: ignore-managed` |
| `.codeiumignore` | Windsurf | `# windsurf: ignore-managed` … `# /windsurf: ignore-managed` |
| `.clineignore` | Cline | `# cline: ignore-managed` … `# /cline: ignore-managed` |
| `.geminiignore` | Gemini CLI | `# gemini: ignore-managed` … `# /gemini: ignore-managed` |
| `.antigravityignore` | Antigravity | `# antigravity: ignore-managed` … `# /antigravity: ignore-managed` |

Replace the marked block. Leave every line outside it. Append the block if the markers are missing. Create the file if it is missing.

`.aiexclude` (Gemini Code Assist and Android Studio) cannot un-ignore with `!`. Do not write `.env.*` or any `!` line into it. Write `.env`, `.env.local`, `.env.development`, `.env.production`, `.env.test`, and `.env.staging`, plus the secrets and stack directories. Markers: `# aiexclude: ignore-managed` and `# /aiexclude: ignore-managed`. Never create an empty `.aiexclude`. An empty one excludes the whole tree.

Cline's file is deprecated and is not a security boundary. Still write it, and say that in the summary.

## Claude Code

File: `.claude/settings.json`. A `.claudeignore` file does nothing. Do not create one.

If the JSON is missing, create `{ "permissions": { "deny": [] } }`. If the file exists and is not valid JSON, or if it contains comments, stop for this file and report that. Do not rewrite it.

Keep every existing key. In `permissions.deny`, remove only rules that exactly match a rule this skill manages, then add the current set. Leave every other deny, ask, and allow rule.

Managed rules, in this order. Deny rules first, then the `!` carve-outs. A `!` rule only carves out an earlier deny in this same file.

```text
Read(.env)
Read(.env.*)
Read(!.env.example)
Read(!.env.sample)
Read(!.env.template)
Read(*.pem)
Read(*.key)
Read(id_rsa)
Read(credentials.json)
Read(secrets.json)
```

Then one `Read(<dir>/**)` for each stack directory that survived the skip rules, such as `Read(node_modules/**)` or `Read(vendor/**)`.

These rules cover Claude's file tools and shell commands it recognizes, such as `cat`. They do not cover a search that never names the file, or a script that opens the file itself.

## Codex

File: `.codex/config.toml`. Permission profiles are beta and do not apply while `sandbox_mode` or `sandbox_workspace_write` is set.

- If either sandbox key is present, write nothing and report that.
- If `default_permissions` is `:danger-full-access`, write nothing and report that.
- If `default_permissions` is another custom profile name, add the deny table to that profile. Do not change `default_permissions`.
- If `default_permissions` is missing, `:workspace`, or `:read-only`, write a profile named `ignore-managed` that extends that built-in (`:workspace` when the key is missing) and set `default_permissions = "ignore-managed"`. Report that this selects the profile.

Add only this table, replacing a previous `# codex: ignore-managed` block when one exists. Do not change network, features, or other filesystem keys.

```toml
# codex: ignore-managed
"**/.env" = "deny"
"**/.env.*" = "deny"
"**/.env.example" = "read"
"**/.env.sample" = "read"
"**/.env.template" = "read"
"**/*.pem" = "deny"
"**/*.key" = "deny"
"**/id_rsa" = "deny"
"**/credentials.json" = "deny"
"**/secrets.json" = "deny"
# /codex: ignore-managed
```

Put stack directories in that same block as `"**/<dir>/**" = "deny"` when they survived the skip rules. The example paths are `read` so they stay available. A broader deny does not cover a more specific path.

When creating the profile, use this shape and nothing else:

```toml
default_permissions = "ignore-managed"

[permissions.ignore-managed]
extends = ":workspace"

[permissions.ignore-managed.filesystem.":workspace_roots"]
```

## Summary

For each file, report **Added**, **Skipped** (with the reason), and **Left in place**. Name Amp, and any Codex case that wrote nothing, under **Skipped**.

Do not claim a terminal, MCP server, or unrecognized shell command is blocked. `.gitignore` is not edited.
