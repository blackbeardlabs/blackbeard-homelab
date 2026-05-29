# Benchmark: Mistral Medium 3.5 128B Huihui Q4_K on node-04-v1 Kraken

## Summary

| Field | Value |
|---|---|
| Node | node-04-v1 (Kraken) |
| Node version | v1 |
| Model | huihui-ai/Huihui-Mistral-Medium-3.5-128B-BF16-abliterated-GGUF |
| Model source | Local GGUF shard path under `~/models/huihui-ai/Huihui-Mistral-Medium-3.5-128B-BF16-abliterated-GGUF/` |
| Loaded file | `Huihui-Mistral-Medium-3.5-128B-BF16-abliterated-Q4_K-00001-of-00008.gguf` |
| Backend | llama.cpp |
| Backend version | 9414 (`da3f990a4`) |
| Precision / quant | Q4_K GGUF, sharded; not a BF16 runtime benchmark |
| Context setting | `-c 80000`; observed slot context `n_ctx = 80128` |
| Prompt id | `prompts/prompt-01-dijkstra-js.md` |
| Status | Observed benchmark run |
| Result summary | Prompt 1 completed through OpenWebUI at 10.55 tok/s generation, but GPU utilization was only about 22-31% in the screenshot. |

## Hardware

| Component | Value |
|---|---|
| CPU | Threadripper 1950X |
| RAM | 128GB DDR4-3200 |
| GPU | 4x RTX 3090 24GB |
| GPU power limit | 250W per GPU |
| Driver | 595.58.03 observed via `nvidia-smi` screenshot |
| CUDA | 13.2 observed via `nvidia-smi` screenshot |
| OS | Linux Mint 22.3 Zena |

## Launch command

```bash
MODEL="$HOME/models/huihui-ai/Huihui-Mistral-Medium-3.5-128B-BF16-abliterated-GGUF/Huihui-Mistral-Medium-3.5-128B-BF16-abliterated-Q4_K-00001-of-00008.gguf"

CUDA_VISIBLE_DEVICES=0,1,2,3 ./build-cuda-20260529-da3f990a4/bin/llama-server \
  -m "$MODEL" \
  --alias mistral-medium-huihui-3.5 \
  --host 0.0.0.0 \
  --port 8080 \
  -ngl 999 \
  --split-mode layer \
  --tensor-split 1,1,1,1 \
  -c 80000 \
  -b 2048 \
  -ub 512 \
  -np 1 \
  -fa on \
  --cache-type-k q8_0 \
  --cache-type-v q8_0 \
  --metrics
```

## Backend build identity

| Field | Value |
|---|---|
| Backend family | llama.cpp / GGUF |
| Backend directory | `/home/blackbeard3/llm-backends/llama.cpp-main` |
| Backend repo / remote | `https://github.com/ggerganov/llama.cpp` |
| Branch | `master` |
| Commit hash | `da3f990a47ec8c25ff3d2154d3dea46ee3f4f334` |
| Last commit | `da3f990a4 mtmd: Add DeepSeekOCR 2 Support (#20975)` |
| Git status | Clean in captured output |
| Build directory | `build-cuda-20260529-da3f990a4` |
| Binary path | `build-cuda-20260529-da3f990a4/bin/llama-server` |
| Binary version output | `llama-server`: version 9414 (`da3f990a4`), built with GNU 13.3.0 for Linux x86_64 |
| Runtime/library status | `llama-server` and `llama-cli` version commands worked. `llama-bench --version` is not supported and returned usage after CUDA device init. |
| Important build flags | `CMAKE_BUILD_TYPE=Release`, `GGML_CUDA=ON`, `GGML_CUDA_FA=ON`, `GGML_CUDA_GRAPHS=ON`, `GGML_CUDA_NCCL=ON`, `GGML_CUDA_PEER_MAX_BATCH_SIZE=128`, `GGML_CPU=ON`, `GGML_LLAMAFILE=ON` |
| CUDA enabled | Yes |
| Notes | Build identity captured from `~/llm-backends/llama.cpp-main`; full selected backend identity excerpt is in the raw log. The upstream directory/file name includes `BF16`, but the loaded benchmark artifact is the quantized `Q4_K` GGUF shard. |

## Request method and sampling

| Field | Value |
|---|---|
| Request method | OpenWebUI |
| Client | OpenWebUI connected to llama.cpp OpenAI-compatible endpoint |
| Sampling params | OpenWebUI defaults |
| Temperature | Not explicitly captured |
| Top-p | Not explicitly captured |
| Top-k | Not explicitly captured |
| Max tokens | Not explicitly captured |
| Streaming | Not explicitly captured |
| Notes | Exact OpenWebUI sampling settings were not captured for this run; performance results should be interpreted as an observed interactive OpenWebUI run, not a fully controlled direct API run. |

## Measured results

| Metric | Value |
|---|---:|
| Prompt eval tok/s | 254.50 tok/s for 451 prompt tokens |
| Generation tok/s | 10.55 tok/s for 785 generated tokens |
| Total time | 76155.80 ms for 1236 tokens |
| VRAM usage | Screenshot: GPU0 ~23207MiB, GPU1 ~21581MiB, GPU2 ~21581MiB, GPU3 ~23223MiB |
| RAM usage | Not explicitly captured beyond startup free-memory log |
| GPU utilization | Screenshot: about 31%, 22%, 22%, 30% |
| CPU utilization | Not captured |
| KV cache size | Slot context `n_ctx = 80128`; prompt cache state saved 1235 tokens / 225.548 MiB after main response |

## Run: 2026-05-29 prompt-01 Dijkstra

| Field | Value |
|---|---|
| Prompt | `prompts/prompt-01-dijkstra-js.md` |
| Request status | Completed |
| Main response timing | Prompt eval 451 tokens at 254.50 tok/s; generation 785 tokens at 10.55 tok/s |
| Server log window | Main response from task 0; follow-up task 786 appears after prompt cache save |
| Prefix/prompt cache | Prompt cache enabled, size limit 8192 MiB |
| Raw log | `raw-logs/2026-05-29-node-04-mistral-medium-huihui-prompt-01-dijkstra-llamacpp.log` |
| Response | `responses/node-04-kraken-4x3090/2026-05-29-mistral-medium-huihui-prompt-01-dijkstra.md` |
| Notes | Low GPU utilization suggests a bottleneck or llama.cpp multi-GPU scaling issue in this configuration. |

## Stability notes

| Area | Result |
|---|---|
| Load stability | Model loaded and server listened on port 8080 |
| Inference stability | Prompt 1 completed |
| Post-response cleanup | Slot released and all slots returned idle after main response and follow-up task |
| Manual intervention needed | None recorded |
| Known bad params | Not established |
| Known stable params | This run used `-ngl 999`, `--split-mode layer`, `--tensor-split 1,1,1,1`, `-c 80000`, `-b 2048`, `-ub 512`, `-np 1`, `-fa on`, `q8_0` KV cache |

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

- The response used JavaScript and returned `distance` plus `path`.
- The response included an example and a concise explanation.
- Correctness issue: the example expected output says distance `5`, but the provided graph has path `A -> B -> C -> D` with weight `1 + 2 + 1 = 4`.
- Correctness issue: the unreachable example calls target `E`, but the implementation initializes only nodes present in `graph`; `distances[target]` is `undefined`, not `Infinity`, and path reconstruction can loop incorrectly because `previous[target]` is also undefined.
- Caveat: negative edge weights are not rejected; Dijkstra requires non-negative weights.

## Observations

- This is the first recorded llama.cpp / GGUF run on Kraken.
- Throughput was much lower than the Qwen3.6 27B BF16 vLLM runs, but the model is much larger and uses a different backend/quantization path.
- Screenshot GPU utilization around 22-31% indicates the run did not keep the GPUs fully busy.
- Follow-up task 786 appears after prompt cache save and likely represents OpenWebUI/UI-layer activity; it is not used as the main benchmark timing.
