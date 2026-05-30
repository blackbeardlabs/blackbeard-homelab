# Benchmark: Qwen3.6 27B Heretic BF16 on node-04-v1 Kraken

## Summary

| Field | Value |
|---|---|
| Node | node-04-v1 (Kraken) |
| Node version | v1 |
| Model | llmfan46/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved |
| Model source | Local model path (`~/models/llmfan46/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved`) |
| Backend | vLLM |
| Backend version | 0.21.0 |
| Precision / quant | BF16 safetensors |
| Context setting | 260000 |
| Prompt id | `prompts/prompt-01-dijkstra-js.md`, `prompts/prompt-02-heterogeneous-scheduler.md` |
| Status | Observed benchmark runs |
| Result summary | Prompt 1 completed at roughly 29.5 tok/s and prompt 2 completed at roughly 27.7 tok/s over steady active samples; stable at `gpu_memory_utilization=0.94`. |

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
CUDA_VISIBLE_DEVICES=0,1,2,3 \
vllm serve ~/models/llmfan46/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved \
  --served-model-name qwen36-27b-bf16-mtp-heretic-260k \
  --host 0.0.0.0 \
  --port 8000 \
  --tensor-parallel-size 4 \
  --dtype bfloat16 \
  --max-model-len 260000 \
  --gpu-memory-utilization 0.94 \
  --reasoning-parser qwen3 \
  --language-model-only \
  --speculative-config '{"method":"mtp","num_speculative_tokens":2}' \
  --disable-custom-all-reduce
```

## Request method and sampling

| Field | Value |
|---|---|
| Request method | OpenWebUI |
| Client | OpenWebUI container connected to vLLM OpenAI-compatible endpoint |
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
| Prompt eval tok/s | Prompt 1: 10.0 tok/s initial sample, later 148.3 tok/s activity sample; Prompt 2: 15.4 tok/s initial sample, later 266.5 tok/s activity sample |
| Generation tok/s | Prompt 1: ~29.5 tok/s steady average; Prompt 2: ~27.7 tok/s steady average |
| Total time | Prompt 1 active about 12:56:21-13:01:41; Prompt 2 active about 14:12:41-14:16:41 |
| VRAM usage | Prompt 1: ~23.35-23.80GB per GPU; Prompt 2: ~23.35-23.84GB per GPU |
| RAM usage | Pending |
| GPU utilization | 97-100% in screenshot during generation |
| CPU utilization | Pending |
| KV cache size | Prompt 1 peak observed 4.7%; Prompt 2 peak observed 3.7%; both returned to 0.0% after completion |

## Run: 2026-05-29 prompt-01 Dijkstra

| Field | Value |
|---|---|
| Prompt | `prompts/prompt-01-dijkstra-js.md` |
| Request status | Completed, HTTP 200 |
| Server log window | 12:56:21-13:01:51 |
| Steady generation samples | 28 samples after initial prompt processing and before tail cleanup |
| Steady generation average | ~29.5 tok/s |
| Steady generation range | 26.7-32.4 tok/s |
| Prefix cache hit rate | 0.0% |
| Speculative decoding | MTP, 2 speculative tokens |
| Draft acceptance rate | Mostly ~65-94% depending on phase |
| Raw log | `raw-logs/2026-05-29-node-04-llmfan46-qwen36-heretic-prompt-01-dijkstra.log` |
| Response | `responses/node-04-kraken-4x3090/2026-05-29-llmfan46-qwen36-heretic-prompt-01-dijkstra.md` |

## Run: 2026-05-29 prompt-02 heterogeneous scheduler

| Field | Value |
|---|---|
| Prompt | `prompts/prompt-02-heterogeneous-scheduler.md` |
| Request status | Completed, HTTP 200 |
| Server log window | 14:12:41-14:16:51 |
| Steady generation samples | 18 main-response samples after initial prompt processing and before tail/UI-layer activity |
| Steady generation average | ~27.7 tok/s |
| Steady generation range | 24.1-30.6 tok/s |
| Prefix cache hit rate | 0.0% |
| Speculative decoding | MTP, 2 speculative tokens |
| Draft acceptance rate | Mostly ~50-80% during main response, with a later 95.7% tail sample |
| Raw log | `raw-logs/2026-05-29-node-04-llmfan46-qwen36-heretic-prompt-02-scheduler.log` |
| Response | `responses/node-04-kraken-4x3090/2026-05-29-llmfan46-qwen36-heretic-prompt-02-scheduler.md` |
| Notes | The later 14:16:11 prompt-throughput spike may reflect OpenWebUI/UI-layer activity; it is not included in the main steady average. |

## Stability notes

| Area | Result |
|---|---|
| Load stability | Stable profile observed in 0.94-0.95 range depending on run |
| Inference stability | Prompts 1 and 2 completed successfully |
| Post-response cleanup | KV cache returned to 0.0% and running requests returned to 0 in both recorded logs |
| Manual intervention needed | None observed for this request |
| Known bad params | `gpu_memory_utilization=0.96` produced CUDA OOM during sampler/warmup path |
| Known stable params | Start from `gpu_memory_utilization=0.94`, then test upward |

## Prompt used

Primary recorded prompts:

- `prompts/prompt-01-dijkstra-js.md`
- `prompts/prompt-02-heterogeneous-scheduler.md`

Prompt 1:

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

Prompt 2:

```text
Design an efficient strategy for scheduling jobs on a small compute cluster with heterogeneous machines.

Scenario:
- Some machines are fast but have limited memory.
- Some machines are slower but have much larger memory.
- Some jobs are short and latency-sensitive.
- Some jobs are long and require large context or large models.
- Only one model can run on a node at a time.

Task:
Propose an algorithm or decision framework for assigning jobs to nodes.

Requirements:
- Explain the strategy clearly.
- Include the factors used in scheduling decisions.
- Discuss tradeoffs and failure cases.
- Provide simple pseudocode for the scheduler.
- Be practical rather than theoretical.
```

## Answer notes

- The answer produced JavaScript, not TypeScript.
- It returned both distance and path.
- It included an example and expected output.
- It handled unreachable target nodes in the documented return shape.
- Caveat: the implementation assumes neighbor nodes are present in the adjacency list or are the target. A neighbor-only node that is not already initialized would not be relaxed correctly.
- Caveat: negative edge weights are not rejected; Dijkstra requires non-negative weights.
- Prompt 2 produced a practical greedy priority/affinity scheduler with factors, tradeoffs, failure cases, and pseudocode.

## Observations

- Model architecture and MTP were recognized by vLLM.
- 0.96 failure appears to be VRAM headroom, not model incompatibility.
- Prompt 1 and prompt 2 results were usable, but quality scoring remains deferred.
- Post-response cleanup was clean in this run; continue tracking across longer runs.
