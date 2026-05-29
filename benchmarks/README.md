# Benchmarks

Each benchmark file should reference an exact node version and prompt id.

Raw logs should be preserved when possible.

Benchmarks should separate:

- Load behavior
- Inference speed
- Memory usage
- Utilization
- Cleanup/stability behavior
- Notes

Do not hide failed runs; failed runs are useful data.

## Request method policy

Direct API/curl runs are preferred for cleaner reproducibility.

OpenWebUI runs are still useful because they represent real interactive usage. OpenWebUI can apply default sampling settings, streaming behavior, title generation, follow-up generation, or other UI-layer behavior.

If exact sampling settings are not captured, write `OpenWebUI defaults` and `Not explicitly captured`. Do not infer or invent missing sampling values.

If OpenWebUI follow-up generation or title generation causes extra backend activity, note it under stability/cleanup behavior.

For controlled future runs, prefer direct API requests with a recorded payload, for example:

```bash
curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "MODEL_NAME",
    "messages": [
      { "role": "user", "content": "PROMPT_TEXT" }
    ],
    "temperature": 0.2,
    "max_tokens": 5000
  }'
```
