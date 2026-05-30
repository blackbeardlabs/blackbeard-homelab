# Benchmark: MiniMax M2.7 Q6_K on node-03-v1 RTX 5090

## Summary

| Field | Value |
|---|---|
| Node | node-03-v1 |
| Node version | v1 |
| Model | lmstudio-community/MiniMax-M2.7-GGUF |
| Model source | Local GGUF shard path under `/mnt/5e8a92dc-efd9-4671-90c7-cb3ba7348757/share/Models/lmstudio-community/MiniMax-M2.7-GGUF/` |
| Loaded file | `MiniMax-M2.7-Q6_K-00001-of-00005.gguf` |
| Backend | llama.cpp mainline |
| Backend version | 9413 (`6ed481eea`) |
| Precision / quant | Q6_K GGUF, sharded |
| Context setting | `-c 100000`; observed slot context `n_ctx = 100096` |
| Prompt id | `prompts/prompt-01-dijkstra-js.md` |
| Status | Observed benchmark run |
| Result summary | Direct curl run completed with `finish_reason=stop`, generating 2206 tokens at 10.15 tok/s. Response was complete and example output was correct, with minor API-shape caveats for unreachable handling and target validation. |

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
cd ~/llm-backends/llama.cpp-main

MINIMAX="/mnt/5e8a92dc-efd9-4671-90c7-cb3ba7348757/share/Models/lmstudio-community/MiniMax-M2.7-GGUF/MiniMax-M2.7-Q6_K-00001-of-00005.gguf"

CUDA_VISIBLE_DEVICES=0 \
OMP_NUM_THREADS=8 \
GOMP_CPU_AFFINITY="0 2 4 6 8 10 12 14" \
./build-cuda-20260529/bin/llama-server \
  -m "$MINIMAX" \
  -ngl 999 \
  --n-cpu-moe 58 \
  --no-mmap \
  -c 100000 \
  -ctk q8_0 -ctv q8_0 \
  -fa on \
  --main-gpu 0 \
  -t 8 --prio 3 \
  --host 0.0.0.0 --port 8083
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
| Request payload | `raw-logs/2026-05-30-node-03-lmstudio-community-minimax-m27-q6k-prompt-01-dijkstra-direct.log` |
| Response JSON | Saved on the node at `~/blackbeard-bench-runs/MiniMax-M2.7-Q6_K-5090-prompt01-dijkstra-response.json`; repo stores response text and metadata in `responses/node-03-rtx5090/2026-05-30-lmstudio-community-minimax-m27-q6k-prompt-01-dijkstra-direct.md` |
| Notes | Request payload used manually chosen model string `MiniMax-M2.7-Q6_K-5090`; response returned model string `MiniMax-M2.7-Q6_K-00001-of-00005.gguf`. Benchmark identity uses the loaded GGUF file. |

## Measured results

| Metric | Value |
|---|---:|
| Prompt eval tok/s | 36.55 tok/s for 122 prompt tokens |
| Generation tok/s | 10.15 tok/s for 2206 generated tokens |
| Total time | 220718.54 ms for 2328 tokens |
| VRAM usage | Screenshots: 29089MiB / 32607MiB before/idle view; 29165MiB / 32607MiB during run |
| RAM usage | Screenshots: about 170.2-170.5GiB / 249.3GiB memory used; swap about 768-769MiB / 2.0GiB |
| GPU utilization | Screenshots: 0% before/idle view; about 5% during run |
| CPU utilization | Screenshot showed selected CPU cores pegged during generation, consistent with CPU MoE/offload involvement |
| KV cache size | Prompt cache enabled, size limit 8192 MiB; context checkpoints enabled |

## Run: 2026-05-30 prompt-01 Dijkstra direct curl

| Field | Value |
|---|---|
| Prompt | `prompts/prompt-01-dijkstra-js.md` |
| Request status | Completed |
| Finish reason | `stop` |
| Output channel behavior | `message.content` populated; `message.reasoning_content` contained a planning note |
| Main response timing | Prompt eval 122 tokens at 36.55 tok/s; generation 2206 tokens at 10.15 tok/s |
| Prompt cache | Cold for prompt content in API metadata; `cached_tokens=0` |
| Raw log | `raw-logs/2026-05-30-node-03-lmstudio-community-minimax-m27-q6k-prompt-01-dijkstra-direct.log` |
| Response | `responses/node-03-rtx5090/2026-05-30-lmstudio-community-minimax-m27-q6k-prompt-01-dijkstra-direct.md` |
| Notes | Completed answer. Example shortest-path output is correct for the provided graph. |

## Stability notes

| Area | Result |
|---|---|
| Load stability | Model loaded and server listened on port 8083 |
| Inference stability | Request completed with `finish_reason=stop` |
| Post-response cleanup | Slot released and server reported all slots idle |
| Manual intervention needed | None recorded during run |
| Warnings | Server logged `special_eos_id is not in special_eog_ids` and `render_message_to_json` template warnings; run still completed |
| Known bad params | Not established |
| Known stable params | This run used `-ngl 999`, `--n-cpu-moe 58`, `--no-mmap`, `-c 100000`, `q8_0` KV cache, `-fa on`, `-t 8`, `--prio 3` |

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
- The implementation uses JavaScript and returns `distance` plus `path` for reachable paths.
- The example graph and expected output are internally consistent: `A -> B -> C -> D` has distance `1 + 2 + 1 = 4`.
- Caveat: the response returns `null` for unreachable paths instead of always returning an object with `distance` and `path`.
- Caveat: the implementation throws if `target` is not present as a graph key, so neighbor-only sink target nodes are not handled as robustly as possible.

## Observations

- Throughput is close to the MiMo V2.5 MXFP4_MOE run on the same node, with MiniMax at 10.15 tok/s and MiMo at 10.56 tok/s under similar 100k-context llama.cpp settings.
- MiniMax generated a longer response than MiMo for the same prompt, but still completed within `max_tokens=2500`.
- GPU utilization remained low in screenshots despite high VRAM usage, suggesting CPU/MoE/offload or memory movement bottlenecks in this configuration.
- This is the first recorded node-03 MiniMax M2.7 Q6_K direct curl run.
