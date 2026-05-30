# Benchmark: Qwen3.6 27B Heretic NVFP4 MTP on node-03-v1 RTX 5090

## Summary

| Field | Value |
|---|---|
| Node | node-03-v1 |
| Node version | v1 |
| Model | llmfan46/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved-NVFP4-GGUF |
| Model source | Local GGUF path under `/mnt/5e8a92dc-efd9-4671-90c7-cb3ba7348757/share/Models/llmfan46/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved-NVFP4-GGUF/` |
| Loaded file | `Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved-NVFP4-MLP-Only-Q8_0.gguf` |
| Backend | llama.cpp mainline |
| Backend version | 9413 (`6ed481eea`) |
| Precision / quant | NVFP4 GGUF, MLP-only Q8_0 file, MTP preserved |
| Context setting | `-c 150000`; observed slot context `n_ctx = 150016` |
| Prompt id | `prompts/prompt-01-dijkstra-js.md` |
| Status | Observed benchmark run |
| Result summary | Cold-cache direct curl run generated 2500 tokens at 147.19 tok/s with `draft-mtp` speculative decoding, but hit `finish_reason=length` with reasoning-only output and no final answer in `message.content`. |

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
MODEL="/mnt/5e8a92dc-efd9-4671-90c7-cb3ba7348757/share/Models/llmfan46/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved-NVFP4-GGUF/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved-NVFP4-MLP-Only-Q8_0.gguf"

cd ~/llm-backends/llama.cpp-main

CUDA_VISIBLE_DEVICES=0 ./build-cuda-20260529/bin/llama-server \
  -m "$MODEL" \
  -ngl 999 \
  -c 150000 \
  -np 1 \
  -fa on \
  --spec-type draft-mtp \
  --spec-draft-n-max 3 \
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
| Request payload | `raw-logs/2026-05-30-node-03-llmfan46-qwen36-heretic-nvfp4-mtp-prompt-01-dijkstra-direct.log` |
| Response JSON | Saved on the node at `~/blackbeard-bench-runs/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved-NVFP4-MLP-Only-Q8_0-5090-prompt01-dijkstra-response.json`; repo stores response text and metadata in `responses/node-03-rtx5090/2026-05-30-llmfan46-qwen36-heretic-nvfp4-mtp-prompt-01-dijkstra-direct.md` |
| Notes | Request payload used manually chosen model string `Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved-NVFP4-MLP-Only-Q8_0-5090`; response returned model string `Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved-NVFP4-MLP-Only-Q8_0.gguf`. |

## Measured results

| Metric | Value |
|---|---:|
| Prompt eval tok/s | 573.91 tok/s for 100 prompt tokens |
| Generation tok/s | 147.19 tok/s for 2500 generated tokens |
| Total time | 17159.01 ms for 2600 tokens |
| Draft acceptance | 0.75413; 1733 accepted / 2298 generated |
| Draft MTP stats | `#calls(b,g,a) = 1 766 766`; generated drafts 766; accepted drafts 685; generated tokens 2298; accepted tokens 1733 |
| VRAM usage | Screenshot: 31167MiB / 32607MiB |
| GPU utilization | Screenshot: 96-97% |
| GPU power | Screenshot: 493W / 500W |
| GPU temperature | Screenshot: 54C |
| KV cache size | Prompt cache enabled, size limit 8192 MiB; context checkpoints enabled |

## Run: 2026-05-30 prompt-01 Dijkstra direct curl

| Field | Value |
|---|---|
| Prompt | `prompts/prompt-01-dijkstra-js.md` |
| Request status | Completed but length-limited |
| Finish reason | `length` |
| Output channel behavior | `message.content` empty; `message.reasoning_content` populated and truncated before final answer |
| Main response timing | Prompt eval 100 tokens at 573.91 tok/s; generation 2500 tokens at 147.19 tok/s |
| Prompt cache | Cold for prompt content in API metadata; `cached_tokens=0` |
| Raw log | `raw-logs/2026-05-30-node-03-llmfan46-qwen36-heretic-nvfp4-mtp-prompt-01-dijkstra-direct.log` |
| Response | `responses/node-03-rtx5090/2026-05-30-llmfan46-qwen36-heretic-nvfp4-mtp-prompt-01-dijkstra-direct.md` |
| Notes | Strong speed run, but not a completed answer-quality run because output stopped inside reasoning. |

## Stability notes

| Area | Result |
|---|---|
| Load stability | Model loaded and server listened on port 8083 |
| Inference stability | Request completed without crash |
| Post-response cleanup | Slot released and server reported all slots idle |
| Manual intervention needed | None recorded during run |
| Warnings | `common_fit_params` could not auto-fit because `-ngl 999` was manually set; run still loaded. `--cache-idle-slots requires --kv-unified, disabling` was logged. |
| Known bad params | Not established |
| Known stable params | This run used `-ngl 999`, `-c 150000`, `-np 1`, `-fa on`, `--spec-type draft-mtp`, `--spec-draft-n-max 3` |

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

- The response did not reach a final JavaScript answer within `max_tokens=2500`.
- `message.content` was empty and `message.reasoning_content` contained planning/code-construction reasoning.
- The run is useful as a throughput and speculative decoding benchmark, not as a completed answer-quality benchmark.
- A separate extended `max_tokens=5000` or `10000` run would be needed to evaluate completed answer quality for this prompt/model combination.

## Observations

- This is the fastest recorded node-03 direct curl generation run so far in this repository.
- `draft-mtp` was active and materially contributed to throughput, with 75.4% token acceptance in the cold-cache run.
- GPU utilization and power draw were high in the screenshot, unlike the MiMo and MiniMax MoE/offload runs on the same node.
- Prompt eval is not directly comparable to the MiMo/MiniMax 100k runs because this launch uses a different model, context size, and speculative decoding profile.
