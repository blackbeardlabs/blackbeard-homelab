# Benchmark: MODEL on NODE

## Summary

| Field | Value |
|---|---|
| Node |  |
| Node version |  |
| Model |  |
| Model source |  |
| Backend |  |
| Backend version |  |
| Precision / quant |  |
| Context setting |  |
| Prompt id |  |
| Status |  |
| Result summary |  |

## Hardware

| Component | Value |
|---|---|
| CPU |  |
| RAM |  |
| GPU |  |
| Driver |  |
| CUDA |  |
| OS |  |

## Launch command

```bash
# command here
```

## Backend build identity

| Field | Value |
|---|---|
| Backend family |  |
| Backend directory |  |
| Backend repo / remote |  |
| Branch |  |
| Commit hash |  |
| Last commit |  |
| Git status |  |
| Build directory |  |
| Binary path |  |
| Binary version output |  |
| Runtime/library status |  |
| Important build flags |  |
| CUDA enabled |  |
| Notes |  |

For llama.cpp-based runs, record the exact fork/build identity. Do not write only `llama.cpp`.

At minimum, include the git remote URL, branch, commit hash, build directory, binary path, and important CMake flags.

If `llama-server --version` or similar commands fail due to missing shared libraries, record the error exactly under `Binary version output` and set `Runtime/library status` to the observed issue. Do not silently omit it.

If the benchmark run itself succeeds despite the version command failing, explain how the working binary was launched.

## Request method and sampling

| Field | Value |
|---|---|
| Request method |  |
| Client |  |
| Sampling params |  |
| Temperature |  |
| Top-p |  |
| Top-k |  |
| Max tokens |  |
| Streaming |  |
| Request payload |  |
| Response JSON |  |
| Notes |  |

For OpenWebUI runs, use `OpenWebUI defaults` when exact sampling parameters were not explicitly captured. Do not invent missing values.

For direct API/curl runs, record the exact JSON payload whenever possible.

Recommended direct API format:

| Field | Value |
|---|---|
| Request method | Direct API / curl |
| Client | Manual curl template from `benchmarks/direct-api-curl.md` |
| Sampling params | Explicit |
| Temperature | `0.2` |
| Top-p | `0.95` |
| Top-k | Not set |
| Max tokens | `2500` |
| Streaming | `false` |
| Request payload | Copy-paste template, recorded in benchmark notes if changed |
| Response JSON | `raw-logs/...response.json` if saved |

For direct API runs, do not write `OpenWebUI defaults`. Record exact values from the JSON payload. If a run uses different sampling settings, record those exact values.

## Measured results

| Metric | Value |
|---|---:|
| Prompt eval tok/s | |
| Generation tok/s | |
| Total time | |
| VRAM usage | |
| RAM usage | |
| GPU utilization | |
| CPU utilization | |
| KV cache size | |

## Stability notes

| Area | Result |
|---|---|
| Load stability | |
| Inference stability | |
| Post-response cleanup | |
| Manual intervention needed | |
| Known bad params | |
| Known stable params | |

## Prompt used

Reference: `prompts/...`

## Observations

- 
