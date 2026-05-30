# Benchmark: MiMo V2.5 MXFP4_MOE on node-03-v1 RTX 5090

## Summary

| Field | Value |
|---|---|
| Node | node-03-v1 |
| Node version | v1 |
| Model | unsloth/MiMo-V2.5-GGUF |
| Model source | Local GGUF shard path under `/mnt/5e8a92dc-efd9-4671-90c7-cb3ba7348757/share/Models/unsloth/MiMo-V2.5-GGUF/` |
| Loaded file | `MiMo-V2.5-MXFP4_MOE-00001-of-00005.gguf` |
| Backend | llama.cpp mainline |
| Backend version | 9413 (`6ed481eea`) |
| Precision / quant | MXFP4_MOE GGUF, sharded |
| Context setting | `-c 100000`; observed slot context `n_ctx = 100096` |
| Prompt id | `prompts/prompt-01-dijkstra-js.md` |
| Status | Observed benchmark run |
| Result summary | Direct curl run completed with `finish_reason=stop`, generating 1300 tokens at 10.56 tok/s. Response was complete but included an incorrect expected output in the example. |

## Hardware

| Component | Value |
|---|---|
| CPU | Ryzen 9 9950X3D |
| RAM | 256GB DDR5-5600 |
| GPU | RTX 5090 32GB |
| GPU power limit | 500W |
| Driver | 595.58.03 observed via `nvidia-smi` screenshot |
| CUDA | 13.2 observed via `nvidia-smi` screenshot |
| OS | Linux Mint 22.3 Zena |

## Launch command

```bash
MIMO_UNSLOTH="/mnt/5e8a92dc-efd9-4671-90c7-cb3ba7348757/share/Models/unsloth/MiMo-V2.5-GGUF/MiMo-V2.5-MXFP4_MOE-00001-of-00005.gguf"

cd ~/llm-backends/llama.cpp-main

CUDA_VISIBLE_DEVICES=0 \
OMP_NUM_THREADS=8 \
GOMP_CPU_AFFINITY="0 2 4 6 8 10 12 14" \
./build-cuda-20260529/bin/llama-server \
  -m "$MIMO_UNSLOTH" \
  -ngl 999 \
  --n-cpu-moe 42 \
  --no-mmap \
  -c 100000 \
  -ctk q8_0 -ctv q8_0 \
  -fa on \
  --main-gpu 0 \
  --jinja \
  -t 8 \
  --prio 3 \
  --host 0.0.0.0 \
  --port 8083
```

## Backend build identity

| Field | Value |
|---|---|
| Backend family | llama.cpp / GGUF |
| Backend directory | `/home/blackbeard/llm-backends/llama.cpp-main` |
| Backend repo / remote | `https://github.com/ggml-org/llama.cpp` |
| Branch | `master` |
| Commit hash | `6ed481eea4cf4ed40777db2fa29e8d08eb712b3b` |
| Last commit | `6ed481eea CUDA: Check PTX version on host side to guard PDL dispatch (#23530)` |
| Git status | Clean in captured output |
| Build directory | `build-cuda-20260529` |
| Binary path | `build-cuda-20260529/bin/llama-server` |
| Binary version output | `llama-server`: version 9413 (`6ed481eea`), built with GNU 13.3.0 for Linux x86_64 |
| Runtime/library status | `llama-server` and `llama-cli` version commands worked. `llama-bench --version` is not supported and returned usage after CUDA device init. |
| Important build flags | `CMAKE_BUILD_TYPE=Release`, `GGML_CUDA=ON`, `GGML_CUDA_FA=ON`, `GGML_CUDA_GRAPHS=ON`, `GGML_CUDA_NCCL=ON`, `GGML_CUDA_PEER_MAX_BATCH_SIZE=128`, `GGML_CPU=ON`, `GGML_LLAMAFILE=ON` |
| CUDA enabled | Yes |
| Notes | Full selected backend identity excerpt is in the raw log. |

## Request method and sampling

| Field | Value |
|---|---|
| Request method | Direct API / curl |
| Client | Manual curl request based on `benchmarks/direct-api-curl.md` |
| Sampling params | Explicit |
| Temperature | `0.2` |
| Top-p | `0.95` |
| Top-k | Not set |
| Max tokens | `2500` |
| Streaming | `false` |
| Request payload | `raw-logs/2026-05-30-node-03-unsloth-mimo-v25-mxfp4-moe-prompt-01-dijkstra-direct.log` |
| Response JSON | Saved on the node at `~/blackbeard-bench-runs/MiMo-V2.5-MXFP4-unsloth-5090-prompt01-dijkstra-response.json`; repo stores response text and metadata in `responses/node-03-rtx5090/2026-05-30-unsloth-mimo-v25-mxfp4-moe-prompt-01-dijkstra-direct.md` |
| Notes | Request payload used manually chosen model string `MiMo-V2.5-MXFP4-unsloth-5090`; response returned model string `MiMo-V2.5-MXFP4_MOE-00001-of-00005.gguf`. Benchmark identity uses the loaded GGUF file. |

## Measured results

| Metric | Value |
|---|---:|
| Prompt eval tok/s | 38.10 tok/s for 106 prompt tokens |
| Generation tok/s | 10.56 tok/s for 1300 generated tokens |
| Total time | 125876.81 ms for 1406 tokens |
| VRAM usage | Screenshot before/early run: 31583MiB / 32607MiB; during run: 31663MiB / 32607MiB |
| RAM usage | Screenshot: about 154.2GiB / 249.3GiB memory used; swap about 597MiB / 2.0GiB |
| GPU utilization | Screenshot before/early run: 0%; during run: about 6% |
| CPU utilization | Screenshot mostly low, with brief activity on selected cores |
| KV cache size | Prompt cache enabled, size limit 8192 MiB; context checkpoints enabled |

## Run: 2026-05-30 prompt-01 Dijkstra direct curl

| Field | Value |
|---|---|
| Prompt | `prompts/prompt-01-dijkstra-js.md` |
| Request status | Completed |
| Finish reason | `stop` |
| Output channel behavior | `message.content` populated; `message.reasoning_content` contained a short planning note |
| Main response timing | Prompt eval 106 tokens at 38.10 tok/s; generation 1300 tokens at 10.56 tok/s |
| Prompt cache | Cold for prompt content in API metadata; `cached_tokens=0` |
| Raw log | `raw-logs/2026-05-30-node-03-unsloth-mimo-v25-mxfp4-moe-prompt-01-dijkstra-direct.log` |
| Response | `responses/node-03-rtx5090/2026-05-30-unsloth-mimo-v25-mxfp4-moe-prompt-01-dijkstra-direct.md` |
| Notes | Completed answer, but example expected output is incorrect. |

## Stability notes

| Area | Result |
|---|---|
| Load stability | Model loaded and server listened on port 8083 |
| Inference stability | Request completed with `finish_reason=stop` |
| Post-response cleanup | Slot released and server reported all slots idle |
| Manual intervention needed | None recorded during run |
| Known bad params | Not established |
| Known stable params | This run used `-ngl 999`, `--n-cpu-moe 42`, `--no-mmap`, `-c 100000`, `q8_0` KV cache, `-fa on`, `--jinja`, `-t 8`, `--prio 3` |

## Prompt used

Reference: `prompts/prompt-01-dijkstra-js.md`

```text
Write a JavaScript function that finds the shortest path in a weighted directed graph using Dijkstra's algorithm.

Requirements:
- Use JavaScript, not TypeScript.
- Input should be an adjacency list.
- Return both the total distance and the actual path.
- Handle unreachable target nodes correctly.
- Include a small example input and expected output.
- Keep the explanation concise.
- Prioritize correctness, clarity, and robustness.
```

## Answer notes

- The response produced a normal final answer in `message.content` and stopped naturally.
- The implementation uses JavaScript and returns `distance` plus `path`.
- Correctness issue: in the provided example graph, the shortest path from `A` to `D` is `A -> B -> D` with distance `3`, but the response expected `A -> B -> E -> F -> D` with distance `4`. The listed path actually totals `6`.
- The response handles `source === target` correctly in the example.
- Caveat: the implementation initializes distances only for graph keys. Neighbor-only sink nodes can still be discovered, but target-only nodes not represented in the graph are treated as unreachable.

## Observations

- Compared with the Step-3.7 Flash Q4_K_S run on the same node, MiMo completed the prompt within `max_tokens=2500`, but generated at about half the speed.
- GPU utilization remained low in screenshots despite high VRAM usage, suggesting CPU/MoE/offload or memory movement bottlenecks in this configuration.
- This is the first recorded node-03 MiMo V2.5 MXFP4_MOE direct curl run.
