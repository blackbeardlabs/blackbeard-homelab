# Benchmark Candidate: Qwen3.6 27B BF16 on node-04-v1 Kraken

## Summary

| Field | Value |
|---|---|
| Node | node-04-v1 (Kraken) |
| Node version | v1 |
| Model | Qwen/Qwen3.6-27B |
| Model source | Local model path (`~/models/Qwen3.6-27B`) |
| Backend | vLLM |
| Backend version | 0.21.0 |
| Precision / quant | BF16 safetensors |
| Context setting | 260000 |
| Prompt id | Pending |
| Status | Pending benchmark / launch profile only |
| Result summary | No completed prompt benchmark is recorded for this non-Heretic BF16 model yet. |

## Hardware

| Component | Value |
|---|---|
| CPU | Threadripper 1950X |
| RAM | 128GB DDR4 |
| GPU | 4x RTX 3090 24GB |
| Driver | Observed, version not pinned in this entry |
| CUDA | Observed, version not pinned in this entry |
| OS | Linux Mint 22.3 Zena |

## Launch command

```bash
CUDA_VISIBLE_DEVICES=0,1,2,3 \
vllm serve ~/models/Qwen3.6-27B \
  --served-model-name qwen36-27b-bf16-mtp-260k \
  --host 0.0.0.0 \
  --port 8000 \
  --tensor-parallel-size 4 \
  --dtype bfloat16 \
  --max-model-len 260000 \
  --gpu-memory-utilization 0.95 \
  --reasoning-parser qwen3 \
  --language-model-only \
  --speculative-config '{"method":"mtp","num_speculative_tokens":2}' \
  --disable-custom-all-reduce
```

## Request method and sampling

| Field | Value |
|---|---|
| Request method | Pending |
| Client | Pending |
| Sampling params | Pending |
| Temperature | Pending |
| Top-p | Pending |
| Top-k | Pending |
| Max tokens | Pending |
| Streaming | Pending |
| Notes | No completed benchmark request is recorded for this model yet. |

## Measured results

| Metric | Value |
|---|---:|
| Prompt eval tok/s | Pending |
| Generation tok/s | Pending |
| Total time | Pending |
| VRAM usage | Pending |
| RAM usage | Pending |
| GPU utilization | Pending |
| CPU utilization | Pending |
| KV cache size | Pending |

## Stability notes

| Area | Result |
|---|---|
| Load stability | Pending |
| Inference stability | Pending |
| Post-response cleanup | Pending |
| Manual intervention needed | Pending |
| Known bad params | Pending |
| Known stable params | Pending; candidate launch profile uses BF16, TP=4, MTP=2, context 260K, `gpu_memory_utilization=0.95` |

## Prompt used

Reference: pending.

## Observations

- This file tracks a candidate launch profile, not a completed benchmark.
- Add measured results only after a prompt run is completed and the request method is recorded.
