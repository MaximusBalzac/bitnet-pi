# bitnet-pi

A single-script launcher for [Microsoft BitNet](https://github.com/microsoft/BitNet) (bitnet.cpp) on a Raspberry Pi (tested on a Pi 5, 16 GB, ARM64).

It wraps the clone, build, model download and run steps so setup is one command.

## Install

```bash
git clone https://github.com/<you>/bitnet-pi.git
cd bitnet-pi
sudo install -m 755 bitnet /usr/local/bin/bitnet   # or copy to ~/bin
bitnet setup
```

`setup` installs clang if missing (uses sudo), clones BitNet, builds it, and downloads
`microsoft/BitNet-b1.58-2B-4T`. It is safe to re-run.

## Usage

| Command | What it does |
|---|---|
| `bitnet setup` | Install, build, download the model (idempotent) |
| `bitnet chat` | Terminal chat |
| `bitnet serve [port]` | OpenAI-compatible server + web UI (default 8081, localhost only) |
| `bitnet stop` | Stop the server |
| `bitnet status` | Show install and server state |
| `bitnet bench` | Tokens/sec benchmark |
| `bitnet update` | Pull latest BitNet and rebuild |

## Configuration

Environment variables: `BITNET_HOME`, `BITNET_MODEL_REPO`, `BITNET_MODEL_DIR`,
`BITNET_THREADS` (default 4), `BITNET_CTX` (default 4096), `BITNET_HOST` (default 127.0.0.1;
set to 0.0.0.0 to expose on the network).

## Notes

- This is a wrapper only; inference is done by Microsoft's BitNet.
- Building takes a while on 4 cores.
