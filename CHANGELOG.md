# Changelog

All notable changes to Blackbeard Labs will be documented in this file.

This project uses `Unreleased` for changes made before a tagged public release.

## Unreleased

### Added

- 2026-05-29: Created initial Markdown repository structure for Blackbeard Labs.
  Area: documentation, node inventory, benchmark notebook, prompt data, raw log handling.
- 2026-05-29: Added `.gitignore` for editor files, local secrets, temporary files, logs, Python caches, and local model caches.
  Area: repository hygiene.
- 2026-05-29: Added agent guidance and changelog tracking.
  Area: documentation.
- 2026-05-29: Added `PROJECTS.md` as a root-level index for apps and experiments built with the local AI setup.
  Area: documentation, project index.
- 2026-05-29: Recorded the node-04 Kraken Qwen3.6 Heretic BF16 vLLM benchmark run for `prompt-01-dijkstra-js`, including raw server log excerpt and observed GPU metrics.
  Area: benchmark data, raw log handling, prompt data.
- 2026-05-29: Allowed committed `.log` files under `raw-logs/` while keeping other logs ignored.
  Area: repository hygiene, raw log handling.
- 2026-05-29: Added verified vLLM `0.21.0` backend version metadata for the node-04 Kraken Qwen3.6 Heretic BF16 benchmark.
  Area: benchmark data, raw log handling.
- 2026-05-29: Added request method and sampling fields to benchmark docs, marked the Heretic Dijkstra run as an OpenWebUI interactive run, and corrected the non-Heretic BF16 entry to pending.
  Area: benchmark data, raw log handling, documentation.
- 2026-05-29: Added response storage and recorded the Qwen3.6 Heretic BF16 Dijkstra benchmark response.
  Area: response data, benchmark data, documentation.
- 2026-05-29: Recorded the node-04 Kraken Qwen3.6 Heretic BF16 OpenWebUI benchmark run for `prompt-02-heterogeneous-scheduler`, including raw log excerpt, response, and observed GPU metrics.
  Area: benchmark data, raw log handling, response data, prompt data.
- 2026-05-29: Expanded agent guidance with the benchmark recording workflow for benchmark files, raw logs, responses, OpenWebUI sampling notes, and throughput summaries.
  Area: documentation, benchmark data, raw log handling, response data.

### Removed

- 2026-05-29: Removed the apps catalog folder; app projects will live in separate repositories with their own docs.
  Area: documentation.
