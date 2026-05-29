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
| Prompt id | `prompts/prompt-01-dijkstra-js.md` |
| Status | Observed benchmark run |
| Result summary | Prompt 1 completed successfully at roughly 29.5 tok/s over steady active samples; stable at `gpu_memory_utilization=0.94`. |

## Hardware

| Component | Value |
|---|---|
| CPU | Threadripper 1950X |
| RAM | 128GB DDR4 |
| GPU | 4x RTX 3090 24GB |
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
| Prompt eval tok/s | 10.0 tok/s at initial sample; later prompt activity sample at 148.3 tok/s |
| Generation tok/s | ~29.5 tok/s average over steady active samples; observed steady range 26.7-32.4 tok/s |
| Total time | Request active from about 12:56:21 to 13:01:41 in server log |
| VRAM usage | ~23.35-23.80GB per GPU in screenshot during generation |
| RAM usage | Pending |
| GPU utilization | 97-100% in screenshot during generation |
| CPU utilization | Pending |
| KV cache size | GPU KV cache usage rose to 4.7%, then returned to 0.0% after completion |

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
| Raw log | `raw-logs/2026-05-29-node-04-qwen36-heretic-prompt-01-dijkstra.log` |

## Stability notes

| Area | Result |
|---|---|
| Load stability | Stable profile observed in 0.94-0.95 range depending on run |
| Inference stability | Prompt 1 completed successfully |
| Post-response cleanup | KV cache returned to 0.0% and running requests returned to 0 in this log |
| Manual intervention needed | None observed for this request |
| Known bad params | `gpu_memory_utilization=0.96` produced CUDA OOM during sampler/warmup path |
| Known stable params | Start from `gpu_memory_utilization=0.94`, then test upward |

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

- The answer produced JavaScript, not TypeScript.
- It returned both distance and path.
- It included an example and expected output.
- It handled unreachable target nodes in the documented return shape.
- Caveat: the implementation assumes neighbor nodes are present in the adjacency list or are the target. A neighbor-only node that is not already initialized would not be relaxed correctly.
- Caveat: negative edge weights are not rejected; Dijkstra requires non-negative weights.

## Observations

- Model architecture and MTP were recognized by vLLM.
- 0.96 failure appears to be VRAM headroom, not model incompatibility.
- Prompt 1 result was usable, but quality scoring remains deferred.
- Post-response cleanup was clean in this run; continue tracking across longer runs.
