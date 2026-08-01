---
name: Call Sail with the Anthropic Messages API
description: Migrate an Anthropic Messages workload to Sail's open-source models, including token counting.
api: openapi/sail-research-openapi-original.json
operations: [createMessage, countMessageTokens, listModels]
---

# Call Sail with the Anthropic Messages API

Sail is Anthropic-compatible: point the Anthropic SDK's base URL at Sail and swap the key.

## Auth
`Authorization: Bearer $SAIL_API_KEY`, base URL `https://api.sailresearch.com/v1`.

## Steps
1. **Pick a model** with `listModels` (`GET /models`) — e.g. `zai-org/GLM-5.2-FP8`, DeepSeek, Kimi, Nemotron.
2. **Estimate cost** first with `countMessageTokens` (`POST /messages/count_tokens`) — returns the input tokens a request would consume without running the model. Respect `429` (rate limited).
3. **Send** with `createMessage` (`POST /messages`) — supports system prompts, tool calling, and SSE streaming. Set an `Idempotency-Key` header to make submission retry-safe.
4. **Choose a completion window** via `metadata.completion_window` to trade latency for lower per-token price.

## Errors
`400`, `401`, `408` (timeout), `429`, `500`. Envelope `{ "error": {...} }`. See errors/sail-research-problem-types.yml.
