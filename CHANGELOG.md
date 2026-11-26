# Changelog

All notable changes to openset-llm. Dates are YYYY-MM-DD.

## Unreleased

- `bench/quick.sh`: a 3-minute speed check that prints a block to paste into a "Measurement on my machine" issue.
- `docker-compose.yml`: `docker compose up` builds the image, keeps the model in a named volume and waits on `/health`.
- `./serve --multi N`: a system prompt the streams share is read once and loaded from disk afterwards (the prefix part
  of agent-loop resume; it was off with `--multi`). Same replies as `--no-resume`, no extra memory. `--multi` now
  writes prefix states to `~/.openset-llm/state` like the single-stream server does.
- A prefix state file that was cut short on disk (for example by a full disk) made the server abort when a request
  tried to load it. Such a file is now detected, removed and written again.

## 0.1.0 (2026-11-25)

First public release.

- A 27B LLM (Qwen3.8-27B as PrismML's ternary Bonsai-2-27B) at 17–20 tokens per second on a CPU-only machine with
  8 cores and 8 GB of RAM: new AVX-512 kernels for the ternary weights, speculative decoding with the model's own
  multi-token-prediction head, a fused attention kernel, several requests in one pass (`--multi`), agent-loop resume.
- One-command install and start (`./start`), a chat page, an OpenAI-compatible server (`./serve`), a Dockerfile, and
  the benchmark harness behind every number in the README (`bench/`).
- `./serve --kv6` and `--kv5` (experimental): 6- and 5-bit attention caches. On an 8 GB machine speculation stays on
  to 12k / 16k context and plain decoding reaches 32k / 64k. Not exact; needs AVX-512; see the README's memory section.
- `./serve` refuses two cache flags together and contexts that cannot fit with `--kv6` / `--kv5` under 12 GB.
- LoRA adapters load through `./serve -- --lora <file>` (documented with measured cost; #1, #2).

Known limits: Linux x86-64 only; the fast path needs AVX-512 VNNI + VBMI; one model; measured on cloud VMs
(Intel Ice Lake, AMD Zen 4 / Zen 5). See the README's Limits section.
