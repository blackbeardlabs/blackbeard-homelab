# Direct API / curl benchmark templates

## Purpose

Direct API/curl runs are preferred for controlled benchmark measurements because the request payload and sampling settings are explicit.

OpenWebUI runs are still useful for real interactive usage, but they may add UI-layer behavior such as follow-up generation, title generation, hidden extra requests, or default sampling settings.

The model server may be running on another machine, such as the RTX 5090 node or Kraken node. These templates are designed to be copied and pasted into the terminal on the node running the model server.

Save the response JSON manually when useful, or copy it into the relevant benchmark and `raw-logs/` files later. Server logs should still be captured separately from the backend terminal.

## Standard direct benchmark settings

| Field | Value |
|---|---|
| temperature | `0.2` |
| top_p | `0.95` |
| max_tokens | `2500` |
| stream | `false` |

These values are defaults for comparable direct API runs.

If a run uses different values, record the exact values in the benchmark file.

Keep `max_tokens` bounded because some models have thinking/reasoning templates and may generate excessive output. For tests that intentionally measure long reasoning, increase `max_tokens` and label the run accordingly.

## Prompt 01: Dijkstra JavaScript benchmark

```bash
curl -sS http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "MODEL_ALIAS_HERE",
    "messages": [
      {
        "role": "user",
        "content": "Write a JavaScript function that finds the shortest path in a weighted directed graph using Dijkstra'\''s algorithm.\n\nRequirements:\n- Use JavaScript, not TypeScript.\n- Input should be an adjacency list.\n- Return both the total distance and the actual path.\n- Handle unreachable target nodes correctly.\n- Include a small example input and expected output.\n- Keep the explanation concise.\n- Prioritize correctness, clarity, and robustness."
      }
    ],
    "temperature": 0.2,
    "top_p": 0.95,
    "max_tokens": 2500,
    "stream": false
  }'
```

Replace `MODEL_ALIAS_HERE` with the server alias, for example `step37-flash-q4ks-5090`.

Replace port `8080` if the server uses another port. For vLLM servers, the port may be `8000`. For llama.cpp servers, the port is often `8080` or a custom port.

## Prompt 01 with response saved to file

```bash
mkdir -p ~/blackbeard-bench-runs

curl -sS http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "MODEL_ALIAS_HERE",
    "messages": [
      {
        "role": "user",
        "content": "Write a JavaScript function that finds the shortest path in a weighted directed graph using Dijkstra'\''s algorithm.\n\nRequirements:\n- Use JavaScript, not TypeScript.\n- Input should be an adjacency list.\n- Return both the total distance and the actual path.\n- Handle unreachable target nodes correctly.\n- Include a small example input and expected output.\n- Keep the explanation concise.\n- Prioritize correctness, clarity, and robustness."
      }
    ],
    "temperature": 0.2,
    "top_p": 0.95,
    "max_tokens": 2500,
    "stream": false
  }' | tee ~/blackbeard-bench-runs/YYYY-MM-DD-node-model-prompt-01-dijkstra-response.json
```

Rename the output file manually for the node, model, date, and prompt.

Copy the saved JSON into the repo's `raw-logs/` folder later if it is worth keeping.

Use `mkdir -p` before `tee`; otherwise the request still runs but the response file will not be written.

## Prompt 02: Heterogeneous scheduler benchmark

```bash
curl -sS http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "MODEL_ALIAS_HERE",
    "messages": [
      {
        "role": "user",
        "content": "Design an efficient strategy for scheduling jobs on a small compute cluster with heterogeneous machines.\n\nScenario:\n- Some machines are fast but have limited memory.\n- Some machines are slower but have much larger memory.\n- Some jobs are short and latency-sensitive.\n- Some jobs are long and require large context or large models.\n- Only one model can run on a node at a time.\n\nTask:\nPropose an algorithm or decision framework for assigning jobs to nodes.\n\nRequirements:\n- Explain the strategy clearly.\n- Include the factors used in scheduling decisions.\n- Discuss tradeoffs and failure cases.\n- Provide simple pseudocode for the scheduler.\n- Be practical rather than theoretical."
      }
    ],
    "temperature": 0.2,
    "top_p": 0.95,
    "max_tokens": 2500,
    "stream": false
  }'
```

## vLLM example

```bash
curl -sS http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen36-27b-bf16-mtp-heretic-260k",
    "messages": [
      {
        "role": "user",
        "content": "Write a JavaScript function that finds the shortest path in a weighted directed graph using Dijkstra'\''s algorithm.\n\nRequirements:\n- Use JavaScript, not TypeScript.\n- Input should be an adjacency list.\n- Return both the total distance and the actual path.\n- Handle unreachable target nodes correctly.\n- Include a small example input and expected output.\n- Keep the explanation concise.\n- Prioritize correctness, clarity, and robustness."
      }
    ],
    "temperature": 0.2,
    "top_p": 0.95,
    "max_tokens": 2500,
    "stream": false
  }'
```

## llama.cpp example

```bash
curl -sS http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "step37-flash-q4ks-5090",
    "messages": [
      {
        "role": "user",
        "content": "Write a JavaScript function that finds the shortest path in a weighted directed graph using Dijkstra'\''s algorithm.\n\nRequirements:\n- Use JavaScript, not TypeScript.\n- Input should be an adjacency list.\n- Return both the total distance and the actual path.\n- Handle unreachable target nodes correctly.\n- Include a small example input and expected output.\n- Keep the explanation concise.\n- Prioritize correctness, clarity, and robustness."
      }
    ],
    "temperature": 0.2,
    "top_p": 0.95,
    "max_tokens": 2500,
    "stream": false
  }'
```
