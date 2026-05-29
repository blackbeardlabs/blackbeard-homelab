# AGENTS.md

Repository guidance for AI agents and automation working on Blackbeard Labs.

## Project scope

- This is a public Markdown-based lab notebook for local LLM inference benchmarks.
- Do not turn this repository into an app project unless explicitly requested.
- Do not add package managers, build systems, frontend frameworks, TypeScript, or generated dependency files.
- Prefer plain Markdown files and small supporting folders.

## Change discipline

- Keep changes practical, technical, and concise.
- Preserve historical benchmark context. Do not rewrite old benchmark results to make newer runs look cleaner.
- Use words like `observed`, `pending`, `hypothesis`, and `known stable profile` when results are not formal measurements.
- Record exact commands when adding benchmark entries.
- Do not commit secrets, tokens, private hostnames, or credentials.

## Changelog requirement

Whenever a change is made in this repository, update `CHANGELOG.md` in the same change set.

Changelog entries should include:

- Date in `YYYY-MM-DD` format.
- Short summary of what changed.
- Relevant files or areas touched.
- Whether the change is documentation, benchmark data, prompt data, node inventory, raw log handling, or project index content.

For pending work before the first release, add entries under `Unreleased`.

## Directory notes

- `nodes/`: Hardware node inventory. Benchmark files should reference exact node versions.
- `prompts/`: Stable benchmark prompts. Avoid casual edits because prompt drift breaks comparisons.
- `benchmarks/`: Benchmark entries and placeholders. Failed runs are useful and should be documented.
- `raw-logs/`: Optional raw backend logs. Keep logs plain text and scrub secrets before committing.
- `PROJECTS.md`: Index of apps and experiments built with the AI home lab. Link to separate app repositories; do not add app source code here.

## Style

- Use clean Markdown.
- Keep sections short.
- Prefer tables for structured benchmark facts.
- Avoid marketing language and inflated claims.
