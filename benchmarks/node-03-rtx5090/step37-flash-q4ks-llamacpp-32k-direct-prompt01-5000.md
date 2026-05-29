# Benchmark: Step-3.7-Flash Q4_K_S on node-03-v1 RTX 5090

## Summary

| Field | Value |
|---|---|
| Node | node-03-v1 |
| Node version | v1 |
| Model | stepfun-ai/Step-3.7-Flash-GGUF |
| Model source | Local GGUF shard path under `/mnt/5e8a92dc-efd9-4671-90c7-cb3ba7348757/share/Models/stepfun-ai/Step-3.7-Flash-GGUF/` |
| Loaded file | `Step-3.7-flash-Q4_K_S-00001-of-00003.gguf` |
| Backend | llama.cpp StepFun fork |
| Backend version | 9355 (`ae0dcaca9`) |
| Precision / quant | Q4_K_S GGUF, sharded |
| Context setting | `-c 32768` |
| Prompt id | `prompts/prompt-01-dijkstra-js.md` |
| Status | Observed benchmark run, length-truncated |
| Result summary | Direct curl run generated 5000 tokens at 20.92 tok/s on RTX 5090, but ended with `finish_reason=length`; response content was empty and output appeared in `reasoning_content`. |

## Hardware

| Component | Value |
|---|---|
| CPU | Ryzen 9 9950X3D |
| RAM | 256GB DDR5-5600 |
| GPU | RTX 5090 32GB |
| GPU power limit | 500W |
| Driver | 595.58.03 observed via `nvidia-smi` screenshot |
| CUDA | 13.2 observed via `nvidia-smi` screenshot |
| OS | Linux, exact distro not captured |

## Launch command

```bash
MODEL="/mnt/5e8a92dc-efd9-4671-90c7-cb3ba7348757/share/Models/stepfun-ai/Step-3.7-Flash-GGUF/Step-3.7-flash-Q4_K_S-00001-of-00003.gguf"

cd ~/llm-backends/llama.cpp-stepfun

CUDA_VISIBLE_DEVICES=0 \
LD_LIBRARY_PATH="$PWD/build-step37/bin:$PWD/build-step37/src:$PWD/build-step37/ggml/src" \
./build-step37/bin/llama-server \
  -m "$MODEL" \
  --alias step37-flash-q4ks-5090 \
  --host 0.0.0.0 \
  --port 8080 \
  -ngl 999 \
  --n-cpu-moe 36 \
  -c 32768 \
  -b 2048 \
  -ub 512 \
  -np 1 \
  -fa on \
  --metrics
```

## Backend build identity

| Field | Value |
|---|---|
| Backend family | llama.cpp / GGUF |
| Backend directory | `/home/blackbeard/llm-backends/llama.cpp-stepfun` |
| Backend repo / remote | `https://github.com/stepfun-ai/llama.cpp.git` |
| Branch | `step3.7` |
| Commit hash | `ae0dcaca9f34babc6ab09c04d541844b1ddb45fb` |
| Last commit | `ae0dcaca9 delete fromjson` |
| Git status | Clean in captured output |
| Build directory | `build-step37` |
| Binary path | `build-step37/bin/llama-server` |
| Binary version output | `llama-server`: version 9355 (`ae0dcaca9`), built with GNU 13.3.0 for Linux x86_64 |
| Runtime/library status | `llama-server` and `llama-cli` version commands worked when `LD_LIBRARY_PATH` was supplied for the server launch. `llama-bench --version` is not supported and returned usage after CUDA device init. |
| Important build flags | `CMAKE_BUILD_TYPE=Release`, `GGML_CUDA=ON`, `GGML_CUDA_FA=ON`, `GGML_CUDA_GRAPHS=ON`, `GGML_CUDA_NCCL=ON`, `GGML_CUDA_PEER_MAX_BATCH_SIZE=128`, `GGML_CPU=ON`, `GGML_LLAMAFILE=ON` |
| CUDA enabled | Yes |
| Notes | StepFun `step3.7` fork required for this model path. Full selected backend identity excerpt is in the raw log. |

## Request method and sampling

| Field | Value |
|---|---|
| Request method | Direct API / curl |
| Client | Manual curl request based on `benchmarks/direct-api-curl.md` |
| Sampling params | Explicit |
| Temperature | `0.2` |
| Top-p | `0.95` |
| Top-k | Not set |
| Max tokens | `5000` |
| Streaming | `false` |
| Request payload | `raw-logs/2026-05-30-node-03-step37-q4ks-prompt-01-dijkstra-direct.log` |
| Response JSON | Saved on the node during capture; repo stores response metadata/excerpt in `responses/node-03-rtx5090/2026-05-30-step37-q4ks-prompt-01-dijkstra-direct-5000.md` |
| Notes | The run used direct curl, not OpenWebUI. The response reached `max_tokens` and returned `finish_reason=length`. |

## Measured results

| Metric | Value |
|---|---:|
| Prompt eval tok/s | 41.05 tok/s for 94 prompt tokens, prompt-cache/checkpoint affected |
| Generation tok/s | 20.92 tok/s for 5000 generated tokens |
| Total time | 241321.31 ms for 5094 tokens |
| VRAM usage | Screenshot: 29047MiB / 32607MiB |
| RAM usage | Not captured |
| GPU utilization | Screenshot: about 12% |
| CPU utilization | Not captured |
| KV cache size | Prompt cache state showed 6 prompts / 2973.165 MiB before the run |

## Run: 2026-05-30 prompt-01 Dijkstra direct curl

| Field | Value |
|---|---|
| Prompt | `prompts/prompt-01-dijkstra-js.md` |
| Request status | Completed transport-level request, length-truncated model output |
| Finish reason | `length` |
| Output channel behavior | `message.content` was empty; generated text appeared in `message.reasoning_content` |
| Main response timing | Prompt eval 94 tokens at 41.05 tok/s; generation 5000 tokens at 20.92 tok/s |
| Prompt cache | Cache/checkpoint affected; log shows `sim_best = 1.000`, restored context checkpoint, and JSON usage shows `cached_tokens=1` |
| Raw log | `raw-logs/2026-05-30-node-03-step37-q4ks-prompt-01-dijkstra-direct.log` |
| Response | `responses/node-03-rtx5090/2026-05-30-step37-q4ks-prompt-01-dijkstra-direct-5000.md` |
| Notes | Treat as a bounded long-generation behavior run, not a clean completed quality answer. |

## Stability notes

| Area | Result |
|---|---|
| Load stability | Server launch command worked on port 8080 |
| Inference stability | Request completed and slot returned idle |
| Post-response cleanup | Slot released; server reported all slots idle |
| Manual intervention needed | None recorded during run |
| Known bad params | `max_tokens=5000` still ended with `finish_reason=length` for this prompt/model behavior |
| Known stable params | This run used `-ngl 999`, `--n-cpu-moe 36`, `-c 32768`, `-b 2048`, `-ub 512`, `-np 1`, `-fa on` |

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

- The model did not produce a normal final answer in `message.content`; it generated reasoning text only.
- The run hit the `5000` token cap with `finish_reason=length`.
- The response had not reached a final JavaScript implementation by the captured endpoint output.
- Quality result: incomplete / failed for normal answer delivery, but useful for measuring Step-3.7 reasoning-heavy behavior and 5090 generation throughput.
- Prompt eval should not be treated as a cold prompt processing measurement because prompt cache/checkpoint reuse was active.

## Observations

- Generation throughput was stable around 20.9 tok/s during the long decode.
- GPU utilization in the screenshot was low relative to power/VRAM usage: about 12%, 103W / 500W, and ~29GB VRAM used.
- This is the first recorded node-03 Step-3.7 Flash Q4_K_S direct curl run.
