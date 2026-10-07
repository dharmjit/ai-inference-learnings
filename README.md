# Weights, Caches and Guesses

An inference guide that works from the transformer block down to the KV cache — model
architecture, prefill and decode, how the KV cache is paged, compressed and offloaded, and
the speculative tricks (draft models, Medusa, EAGLE, n-gram lookup) that turn one expensive
read of the model into several tokens.

**Read it → https://dharmjit.github.io/ai-inference-learnings/**

One self-contained HTML file. No build step, no framework, no dependencies — the only
script drives the contents rail, section folding, the glossary tooltips, the KV cache
calculator in section 06 and the n-gram walk-through in section 16.
Every diagram is inline SVG. The only external request is to Google Fonts.

## What's in it

**Origins** — who introduced each idea, when, and what problem they actually had.

| Part | Sections |
| --- | --- |
| **I · The model** | 01 One block, many times · 02 Attention and its variants (MHA, MQA, GQA, MLA, RoPE) · 03 Mixture of experts |
| **II · Prefill and decode** | 04 Two phases, two bottlenecks (roofline) · 05 Batching is the whole game |
| **III · The KV cache** | 06 What the cache holds (calculator) · 07 Pages, not slabs · 08 Reusing a prefix |
| **IV · Compressing the cache** | 09 Five ways to make it smaller · 10 Fewer bits · 11 Fewer tokens |
| **V · Offloading the cache** | 12 Load or recompute · 13 A cache that outlives the GPU |
| **VI · Speculative decoding** | 14 Guess, then check · 15 Where guesses come from · 16 N-grams: guessing for free (walk-through) |
| **VII · Fitting it on GPUs** | 17 Fewer bits for weights · 18 Splitting a model |
| **VIII · One model, end to end** | 19 One model, every lever — Llama 3.3 70B on H100s |
| **Coda** | Read fewer bytes |

The through-line: to write one token, a model reads every weight and every cached key and
value of the conversation so far. Every technique here either reads fewer bytes per step,
writes more tokens per read, or avoids the read by keeping something it already computed.

## Where to start

The page opens with four reading paths — new to inference, you serve models, you choose
models, or the whole thing top to bottom. Every section also carries the question it
answers, so a collapsed page reads as an index you can scan.

## Reading it locally

```bash
open index.html
```

If you prefer a server:

```bash
python3 -m http.server -d . 8000
```

## Hosting your own copy

GitHub Pages, no workflow needed: **Settings → Pages → Source: Deploy from a branch →
`main` / `(root)`**. It is equally happy on Cloudflare Pages, Netlify, S3, or any static host.

If you fork it, update `og:url` and `link[rel=canonical]` in `index.html`, and the link above.

## Still to come

`sampling & logits processing` · `structured output & constrained decoding` ·
`LoRA adapters at serving time` · `long context: ring & sequence parallelism` ·
`linear attention & state-space layers` · `attention kernels, inside`

## Corrections

Inference moves fast, and this guide picks one answer where a real deployment might pick
another. Conventions:

- Model shapes (layers, heads, head dimension, expert counts) are from the published model configs.
- Throughput, bandwidth and timing figures are **idealised order-of-magnitude estimates** for
  H100-class hardware. They are there to reason about ratios — benchmark before you size anything.
- Engine option names (vLLM, SGLang) change between releases; check the docs for your version.

If something here is wrong, a PR or an issue naming the paper or the code is very welcome.

## History

An earlier version of this repo designed a multi-tenant inference *service* (gateway, keys,
limits, fair-queue scheduler, autoscaling). It lives on the `service-design` branch.

## License

[CC BY 4.0](LICENSE) — use it, remix it, teach from it, keep the attribution.
