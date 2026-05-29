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

## Direct API benchmark runs

Direct curl/API requests are preferred for controlled benchmarks.

Direct curl templates live in `benchmarks/direct-api-curl.md`.

These templates are intended to be copied into the terminal on the node running the model server.

The repo does not assume benchmark scripts are executed from the repo directory.

Server logs should still be captured separately from the model backend terminal.

Response JSON should be copied back into `raw-logs/` manually when useful.

OpenWebUI runs should be labeled separately because OpenWebUI may apply defaults or make extra requests.

## Request method policy

Direct API/curl runs are preferred for cleaner reproducibility.

OpenWebUI runs are still useful because they represent real interactive usage. OpenWebUI can apply default sampling settings, streaming behavior, title generation, follow-up generation, or other UI-layer behavior.

If exact sampling settings are not captured, write `OpenWebUI defaults` and `Not explicitly captured`. Do not infer or invent missing sampling values.

If OpenWebUI follow-up generation or title generation causes extra backend activity, note it under stability/cleanup behavior.

For controlled future runs, prefer direct API requests with a recorded payload, for example:

```bash
curl -sS http://localhost:8000/v1/chat/completions \
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

## Recording llama.cpp backend identity

For llama.cpp-based benchmarks, record the exact fork and build identity. Do not write only `llama.cpp`.

Recommended capture block:

```bash
BACKEND_DIR="$HOME/llm-backends/llama.cpp-main"
BUILD_DIR="build"

cd "$BACKEND_DIR" || exit 1

echo "===== BACKEND IDENTITY ====="
echo "Backend dir: $(pwd)"
echo

echo "===== GIT REMOTE ====="
git remote -v
echo

echo "===== GIT BRANCH ====="
git branch --show-current
echo

echo "===== GIT COMMIT ====="
git rev-parse HEAD
git log -1 --oneline
echo

echo "===== GIT STATUS ====="
git status --short
echo

echo "===== BINARIES ====="
find "$BUILD_DIR" -type f \( -name "llama-server" -o -name "llama-cli" -o -name "llama-bench" \) -print 2>/dev/null
echo

echo "===== LLAMA-SERVER VERSION ====="
"$BACKEND_DIR/$BUILD_DIR/bin/llama-server" --version 2>&1 || true
echo

echo "===== LLAMA-CLI VERSION ====="
"$BACKEND_DIR/$BUILD_DIR/bin/llama-cli" --version 2>&1 || true
echo

echo "===== LLAMA-BENCH VERSION ====="
"$BACKEND_DIR/$BUILD_DIR/bin/llama-bench" --version 2>&1 || true
echo

echo "===== CMAKE BUILD FLAGS ====="
cat "$BUILD_DIR/CMakeCache.txt" 2>/dev/null | grep -E "GGML_CUDA|GGML_CUBLAS|CMAKE_BUILD_TYPE|CMAKE_CUDA|CUDA|LLAMA|GGML" | head -160 || true
```

Change `BACKEND_DIR` and `BUILD_DIR` for each fork/build.

Examples:

- `~/llm-backends/llama.cpp-main`, `build`
- `~/llm-backends/llama.cpp-mtp`, `build`
- `~/llm-backends/llama.cpp-turboquant-kv`, `build-5090-3090`
- `~/llm-backends/llama.cpp-stepfun`, `build-step37`

### Shared library version command failures

Some llama.cpp builds may fail when running `llama-server --version` directly, for example:

```text
error while loading shared libraries: libllama-common.so.0: cannot open shared object file: No such file or directory
```

Record this exactly in the benchmark file. This usually means the binary cannot find its shared library path in that shell context. The benchmark should note whether the actual server command still worked, whether `LD_LIBRARY_PATH` was required, or whether the build should be fixed/rebuilt.

This is benchmark-relevant because different forks and build directories can behave differently.
