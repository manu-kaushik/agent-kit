Turn the short request after `/refine-prompt` into a scoped implementation brief. Do NOT edit files, run write commands, or start the work — only output the brief.

1. Take the text after `/refine-prompt` as the request. If it is empty, ask for a prompt and stop.

2. Ground the brief in the repo, read-only and bounded:
   - Read Overview, Current focus, Stack, Architecture, and Constraints in `SOURCE.md`. If there is no `SOURCE.md`, read `AGENTS.md` or `CLAUDE.md` instead.
   - Run a few targeted searches for the feature area and related mechanisms (e.g. existing retries, idempotency, error classification, logging or metrics, public API surface, test layout). Stop searching once the relevant area is clear.
   - Use only read-only tools and read-only shell commands.

3. Write the brief:
   - First line: one imperative sentence that narrows the request to the concrete behavior (e.g. `Add payment retry` → `Implement retry handling for transient payment-provider failures.`).
   - Then about 5–10 bullets, one constraint each. Cover only what is relevant: what is in scope, what must not be changed, existing mechanisms to reuse, edge cases and safety, contracts to preserve, tests matching the existing test layout, observability, and no unrelated changes.
   - Call a mechanism "existing" only if you found it in the repo, and name it. Otherwise phrase the bullet as a requirement (e.g. `Use an idempotency key so retries cannot double-charge.`).
   - No implementation steps, file-by-file plan, or code.

4. Print the brief in a single fenced code block, then stop. Do not soft-wrap lines inside the code block — keep each line on a single line. If an ambiguity would change the scope, list up to 3 short questions after the block, outside it. Do not start the work, do not ask to start it.
