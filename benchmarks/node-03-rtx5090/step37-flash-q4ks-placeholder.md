# Benchmark Placeholder: Step-3.7-Flash GGUF Q4_K_S on node-03-v1

## Summary

| Field | Value |
|---|---|
| Node | node-03-v1 |
| Node version | v1 |
| Model | stepfun-ai/Step-3.7-Flash-GGUF |
| Precision / quant | Q4_K_S |
| Status | Pending |

## Hypothesis

- RTX 5090 has stronger single-GPU compute and faster DDR5/CPU fallback.
- Only 32GB VRAM is available, so most of a ~112GB model will live outside VRAM.
- Compare measured behavior against Kraken 4x3090.
