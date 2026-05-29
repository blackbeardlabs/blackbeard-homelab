# node-04-v1 (Kraken)

## Profile

| Field | Value |
|---|---|
| Node | node-04-v1 |
| Codename | Kraken |
| Role | Multi-GPU deep-context inference node |
| CPU | Threadripper 1950X |
| RAM | 128GB DDR4 |
| GPU | 4x RTX 3090 24GB |
| Motherboard | Gigabyte X399 Designare EX |
| PCIe | PCIe 3.0 x16/x8/x16/x8 observed |
| Cooling | Open-frame / multi-GPU rig |

## Notes

- Strong aggregate VRAM node.
- vLLM tensor parallel works well with Qwen3.6 27B BF16.
- Known stable profile: Qwen3.6 27B BF16, TP=4, MTP=2, 260K context, `gpu_memory_utilization=0.95`.
- `0.96` can be too close to VRAM limits on some variants.
- Good benchmark target for large GGUF models where aggregate VRAM may beat single-GPU DDR5 fallback.
