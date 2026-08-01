---
name: Run batch inference at scale on Sail
description: Submit many inference requests as one batch, poll its status, and pull per-request results.
api: openapi/sail-research-openapi-original.json
operations: [createBatch, getBatch, listBatches, getBatchRequestResult]
---

# Run batch inference at scale on Sail

For thousands of concurrent requests, use batches instead of firing individual calls. See docs.sailresearch.com/requests_at_scale.

## Auth
`Authorization: Bearer $SAIL_API_KEY`, base URL `https://api.sailresearch.com/v1`.

## Steps
1. **Create** the batch with `createBatch` (`POST /batches`), assigning each line a `custom_id`. Set an `Idempotency-Key` header so a retried submit does not double-run.
2. **Poll status** with `getBatch` (`GET /batches/{batch_id}`) until it reports complete.
3. **List** your batches with `listBatches` (`GET /batches`) using cursor pagination params `after_id`, `before_id`, `limit`.
4. **Fetch results** per request with `getBatchRequestResult` (`GET /batches/{batch_id}/{custom_id}`).

## Errors
`400` malformed batch, `401` auth, `404` unknown batch/custom_id, `500` server. Envelope: `{ "error": {...} }`. See errors/sail-research-problem-types.yml.
