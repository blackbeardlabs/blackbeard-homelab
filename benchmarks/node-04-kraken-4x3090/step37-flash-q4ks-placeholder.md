# Benchmark Placeholder: Step-3.7-Flash GGUF Q4_K_S on node-04-v1 Kraken

## Summary

| Field | Value |
|---|---|
| Node | node-04-v1 (Kraken) |
| Node version | v1 |
| Model | stepfun-ai/Step-3.7-Flash-GGUF |
| Precision / quant | Q4_K_S |
| Status | Pending |

## Hypothesis

- 4x3090 aggregate VRAM may help because Q4_K_S is around 112GB.
- llama.cpp multi-GPU scaling is uncertain and must be measured.
- Compare directly against node-03 RTX 5090 plus DDR5 fallback behavior.

## Backend build identity

| Field | Value |
|---|---|
| Backend family | llama.cpp / GGUF |
| Backend directory | Pending |
| Backend repo / remote | Pending |
| Branch | Pending |
| Commit hash | Pending |
| Last commit | Pending |
| Git status | Pending |
| Build directory | Pending |
| Binary path | Pending |
| Binary version output | Pending |
| Runtime/library status | Pending |
| Important build flags | Pending |
| CUDA enabled | Pending |
| Notes | StepFun's llama.cpp fork / `step3.7` branch may be required. |

## Notes

- StepFun's llama.cpp fork / `step3.7` branch may be required.
- No claim yet on practical throughput or stability.
