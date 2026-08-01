---
name: Run a background agent response on Sail
description: Submit a long-running OpenAI Responses task to Sail, poll for it, and receive a completion webhook.
api: openapi/sail-research-openapi-original.json
operations: [createResponse, getResponse]
---

# Run a background agent response on Sail

Sail runs open-source models for long-horizon / background agents. Use the OpenAI-compatible Responses API.

## Auth
Send `Authorization: Bearer $SAIL_API_KEY` on every request. Base URL is `https://api.sailresearch.com/v1`.

## Steps
1. **Submit** the task with `createResponse` (`POST /responses`). Set `background: true` to get an immediate `202` with a `response_id`; the model runs asynchronously. Pick a cost tier with `metadata.completion_window` (`asap` | `priority` | `standard` | `flex`) — longer windows cost less.
2. **Be retry-safe.** Set an `Idempotency-Key` header (<=255 chars). Retrying with the same key returns the stored response instead of re-running inference. (See conventions/sail-research-conventions.yml.)
3. **Get notified (optional).** Put a `completion_webhook` URL and a `webhook_token` in `metadata`. On completion Sail POSTs the same body as `getResponse`, with `Authorization: Bearer <webhook_token>`. Deduplicate on the response `id`.
4. **Poll / retrieve** with `getResponse` (`GET /responses/{response_id}`) if you did not use a webhook.

## Errors
Envelope is `{ "error": { "message", "type", "param", "code" } }`. Handle `400` (bad request), `401` (auth), `500/502/504` (upstream/model). See errors/sail-research-problem-types.yml.
