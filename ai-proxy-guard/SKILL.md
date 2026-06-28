---
name: ai-proxy-guard
description: Build a server-side proxy for LLM calls with response caching, rate-limiting, context caps, and per-call cost logging — keeping API keys off the client. Use whenever an app calls an LLM.
---

# ai-proxy-guard

Stop apps from calling LLM providers directly from the client (which leaks keys and blows up cost).
Route every model call through a small server-side proxy with guardrails.

## Build
1. **Server-side route** that forwards requests to your LLM provider. The provider API key lives
   ONLY in server env vars — never in the client bundle.
2. **Auth check** — only authenticated/authorised callers can hit the proxy.
3. **Input validation** — validate and bound the request (schema + max input size). Treat user
   free-text as untrusted (consider prompt-injection).
4. **Context cap** — trim the input to the minimum needed (e.g. a recent window), not everything.
5. **Caching** — cache responses for inputs that repeat or change slowly; serve cache hits cheaply.
6. **Rate-limiting** — per-user/IP limits to stop abuse-driven spend.
7. **Cost logging** — log tokens + cost per call so spend is observable and capped.
8. **Batch where possible** — prefer scheduled/batched calls over per-keystroke calls for
   non-interactive features.

## Done when
No key is in any client bundle; cache + rate-limit + cost-cap are verified by tests; a sample
call logs its cost.

## Token tactics
This skill IS token optimisation: caching + context caps + batching are the levers. Build the
proxy once; every feature reuses it. Bake any static prompt templates as files (built once),
not regenerated at runtime.
