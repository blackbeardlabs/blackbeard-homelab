# Blackbeard Labs

Local LLM inference benchmarks on real mixed hardware.

This repository tracks practical local inference behavior across home-lab nodes. It is a lab notebook, not a leaderboard claim set.

## Lab defaults

Unless otherwise noted in a specific benchmark or node file, Linux machines in this lab run `Linux Mint 22.3 Zena`.

## Node overview

| Node id | Role | CPU | RAM | GPU | Current best-known/default model | Notes |
|---|---|---|---|---|---|---|
| node-00-v1 | NAS / archive / utility | Intel i3-7100 | 32GB DDR4 | None | N/A | Model storage and utility services |
| node-01-v1 | Low-end baseline / offload experiments | Ryzen 5 5600 | 64GB DDR4-2733 | GTX 1070 8GB | Small quantized models | MSI A520M-A PRO; minimum practical baseline |
| node-02-v1 | Midrange inference node | Ryzen 9 5950X | 128GB DDR4 | RTX 5060 Ti | Pending | Midrange CUDA test node |
| node-03-v1 | Fast single-GPU / experimental node | Ryzen 9 9950X3D | 256GB DDR5-5600 | RTX 5090 32GB | Qwen3.6 27B NVFP4 variant (pending confirmation) | 500W GPU power limit; high-speed single-GPU and NVFP4 experiments |
| node-04-v1 (Kraken) | Multi-GPU deep-context inference | Threadripper 1950X | 128GB DDR4-3200 | 4x RTX 3090 24GB | Qwen3.6 27B Heretic BF16 via vLLM TP=4 MTP=2 | 250W power limit per GPU; strong aggregate VRAM node |

## Benchmark philosophy

- Run the same prompts across nodes where possible.
- Record exact launch and test commands.
- Separate speed, memory, stability, and subjective quality notes.
- Defer formal quality scoring for now.
- Preserve raw logs for reproducibility.

## Current recorded benchmarks

- `node-04-v1 / Kraken` runs `llmfan46/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved` through vLLM `0.21.0`, tensor parallel 4, `MTP=2`, `260K` context, and `gpu_memory_utilization=0.94`. Recorded OpenWebUI prompt runs averaged roughly `29.5 tok/s` for prompt 1 and `27.7 tok/s` for prompt 2 over steady active generation samples.
- `node-04-v1 / Kraken` also has a llama.cpp / GGUF OpenWebUI run for Huihui Mistral Medium 3.5 128B using the quantized `Q4_K` GGUF shard at `-c 80000`, generating at `10.55 tok/s` on prompt 1. GPU utilization was only about `22-31%` in the screenshot, so this profile needs follow-up tuning.
- `node-03-v1` has a llama.cpp direct-curl run for `llmfan46/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved-NVFP4-GGUF` using the `MLP-Only-Q8_0` GGUF at `-c 150000` with `draft-mtp`, generating at `147.19 tok/s`; the run hit `finish_reason=length` with reasoning-only output.
- `node-03-v1` has a llama.cpp direct-curl run for `lmstudio-community/MiniMax-M2.7-GGUF` using the `Q6_K` shard at `-c 100000`, completing prompt 1 at `10.15 tok/s` generation with `finish_reason=stop`; the response was complete with minor unreachable/target validation caveats.
- `node-03-v1` has a llama.cpp direct-curl run for `unsloth/MiMo-V2.5-GGUF` using the `MXFP4_MOE` shard at `-c 100000`, completing prompt 1 at `10.56 tok/s` generation with `finish_reason=stop`; the response had an incorrect example expected output.
- `node-03-v1` also has a llama.cpp StepFun direct-curl run for `Step-3.7-Flash-GGUF` `Q4_K_S`, generating at `20.92 tok/s` but hitting `finish_reason=length` with reasoning-only output.

## Scope note

Values in this repository are empirical lab observations. Results can change with driver versions, backend versions, model revisions, launch parameters, and workload shape.
