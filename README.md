# Keys, Queues and Caches

A design guide for a multi-tenant, regional LLM inference service — ten models, one
shared fleet, `POST /v1/chat/completions` streamed over SSE to anyone holding an API key.
It works inward from the request path, through the key that guards it, down to the KV
cache on a single replica, and back out to the capacity that keeps the fleet sized.

**Read it → https://dharmjit.github.io/ai-inference-learnings/**

One self-contained HTML file. No build step, no framework, no dependencies — the only
script drives the contents rail, section folding and the glossary tooltips.
Every diagram is hand-authored inline SVG. The only external request is to Google Fonts.

## What's in it

**Origins** — who built each piece of the serving stack, when, and what problem they actually had.

Organised by component, in the order a request meets them — one pill per component.

| Component | Sections |
| --- | --- |
| **Overview** | 01 The shape of it · 02 The latency budget · 03 A region, sized — 1,500 req/s, 18,000 streams, ~240 GPUs |
| **Gateway** | 04 The gateway holds on · 05 Inside the gateway — tables, stream lifecycle, backpressure, retries, catalog, metrics |
| **Key service** | 06 Anatomy of a key · 07 Verifying every request · 08 When a key leaks |
| **Limit service** | 09 One limit, many gateways — demand-weighted shares and their failure bounds |
| **Router** | 10 Choosing a replica |
| **Engine** | 11 Inside one replica · 12 Three layers of isolation · 13 The fair-queue scheduler — admission queue, cost model, parameters, tests |
| **Autoscaler** | 14 What to scale on · 15 Cold start |
| **Fleet arbiter** | 16 Ten models, one fleet |
| **Coda** | Bound it, then spend it locally |

The through-line: every guarantee is a central authority handing a *bounded* piece of
itself to the hot path and taking it back on a timer. Rate limits become per-gateway
bucket shares, key validity becomes a cached entry with a pushed revocation and a TTL,
GPU memory becomes a per-tenant block share, capacity becomes a per-pool floor and ceiling.
The design question each time is the same pair — how big is the piece, and what is the
worst it can cost if the holder vanishes with it.

## Where to start

The page opens with a table of the seven components and four reading paths — you're
building the API, you run the GPUs, you own the platform, or the whole thing top to bottom. Every section also carries the
question it answers, so a collapsed page reads as an index you can scan.

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

`metering & prepaid billing` · `speculative decoding` · `disaggregated prefill & decode` ·
`LoRA multiplexing` · `multi-region failover` · `batch API & offline queues` ·
`structured output & tool calls`

## Corrections

Inference moves fast, and this guide picks one answer where a real deployment might pick
another. Conventions:

- Model architecture figures (layers, KV heads, head dimension) are from the published model configs.
- Throughput, cold-start and latency numbers are **illustrative orders of magnitude** for
  H100/H200-class hardware and current open engines (vLLM, SGLang). Benchmark your own mix
  before sizing a real fleet.

If something here is wrong, a PR or an issue naming the paper or the code is very welcome.

## License

[CC BY 4.0](LICENSE) — use it, remix it, teach from it, keep the attribution.
