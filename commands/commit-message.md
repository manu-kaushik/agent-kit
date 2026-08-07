Generate a commit message for the currently staged changes. Do NOT run `git commit` or any write git command — only output the message.

1. Run `git diff --cached --stat` and `git diff --cached --name-only` to get a file-level summary of staged changes. If nothing is staged, run `git status`, inform the user, and stop. For individual files where the change intent is unclear from the filename alone, read the file's diff with `git diff --cached -- <file>`.

2. Analyze the changes. Produce a single concise conventional commit message:
   - First line: imperative mood, ≤72 chars, no trailing period (e.g. `feat: add dark mode toggle`)
   - If needed, a blank line followed by a short bullet-list body (one line per logical group of changes) explaining *why*, not *what*
   - Use conventional commit prefixes: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`, `style`, `perf`

3. Print the commit message in a fenced code block, then stop. Do not soft-wrap lines inside the code block — keep the subject and each body line on a single line with no mid-line breaks. Do not commit, do not ask to commit.
