# bitnet-pi

A single-script launcher for [Microsoft BitNet](https://github.com/microsoft/BitNet) (bitnet.cpp)
on a Raspberry Pi (tested on a Pi 5, 16 GB, ARM64). It handles the clone, build, model download
and running, so setup is one command.

## Install

```bash
git clone https://github.com/MaximusBalzac/bitnet-pi.git
cd bitnet-pi
sudo install -m 755 bitnet /usr/local/bin/bitnet   # or copy it into ~/bin
bitnet setup
```

`setup` installs clang/cmake/git if missing (uses sudo), clones BitNet at a pinned commit,
builds it, and downloads the 2B-4T model (~1.2 GB). It is safe to re-run. Building takes
about 20-30 minutes on 4 cores.

## Usage

| Command | What it does |
|---|---|
| `bitnet setup` | Install, build, download the model (idempotent) |
| `bitnet chat` | Terminal chat |
| `bitnet serve [port]` | OpenAI-compatible server + web UI (default 8081, localhost only) |
| `bitnet stop` | Stop the server |
| `bitnet status` | Show install and server state |
| `bitnet bench` | Tokens/sec benchmark |

Example:

```bash
bitnet serve
curl http://127.0.0.1:8081/v1/chat/completions -H 'Content-Type: application/json' \
  -d '{"messages":[{"role":"user","content":"Hello"}]}'
```

## Configuration

Environment variables: `BITNET_BASE` (default `~/bitnet-pi`), `BITNET_HOME`, `BITNET_REF`,
`BITNET_MODEL_DIR`, `BITNET_THREADS` (default 4), `BITNET_CTX` (default 4096),
`BITNET_HOST` (default 127.0.0.1; set to 0.0.0.0 to expose on the network).

## Why BitNet is pinned

BitNet is pinned to commit `c17d1c5` (April 2025). On a Pi 5 (Cortex-A76) with the
`microsoft/bitnet-b1.58-2B-4T-gguf` model, measured with this launcher:

- Pinned commit: coherent output, about 9 tokens/s generation and 20 tokens/s prompt processing.
- BitNet from July 2026 onward: the optimized I2_S kernel moved to x86 only, ARM falls back to a
  scalar loop (about 1.3 tokens/s), and the output was garbage in my tests. Its `setup_env.py` also
  failed at the quantize step for this model.
- A build from March 2026 (`01eb415`) was fast but produced garbage output with this model file.

These are results from one Pi 5, not a full compatibility study. Override `BITNET_REF` at your
own risk.

## Notes

- This is a wrapper only; inference is done by Microsoft's BitNet (MIT licensed).
- ARM64 only. x86 would need a different kernel generation step.
