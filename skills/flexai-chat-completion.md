---
name: Call an open model with the FlexAI chat completions API
description: >-
  Use the FlexAI Token Factory OpenAI-compatible API to run a chat completion
  against an open model, including model discovery, auth, streaming, and error
  handling.
api: openapi/flexai-token-factory-openapi.yml
operations: [listModels, createChatCompletion]
---

# Chat completion with FlexAI Token Factory

FlexAI Token Factory is a drop-in OpenAI-compatible API. Point any OpenAI SDK at
`https://tokens.flex.ai/v1` and pass a FlexAI API key.

## Prerequisites
- A FlexAI API key (create one at https://tokens.flex.ai/signup).
- Base URL: `https://tokens.flex.ai/v1`.
- Auth header on every request: `Authorization: Bearer $FLEXAI_API_KEY`.

## Steps

1. **Discover a model** — call `listModels` (`GET /v1/models`) and pick a model
   `id` for your modality (text, code, reasoning, vision, embedding). The
   catalog is live; do not hardcode a model that may be retired.

2. **Create the completion** — call `createChatCompletion`
   (`POST /v1/chat/completions`) with `model` and a `messages` array of
   `{role, content}` objects (roles: system, user, assistant, tool). Optional
   params: `temperature`, `top_p`, `max_tokens`, `stop`, `seed`, `tools`,
   `tool_choice`, `response_format`.

3. **Stream if needed** — set `stream: true` for server-sent events; add
   `stream_options.include_usage: true` to get a final token-usage chunk.

## Rules and gotchas
- **Output cap:** non-streaming responses are capped at 2048 output tokens
  regardless of `max_tokens` (you will see `finish_reason: "length"`). Stream
  for longer outputs.
- **`n` > 1 is rejected** with a 400 — request one choice per call.
- **No idempotency key:** FlexAI does not document `Idempotency-Key`. Make
  retries safe on your side; `seed` only makes sampling reproducible.
- **Rate limits:** 10 rpm (free) / 100 rpm (paid) per key. On `429`, honor the
  `Retry-After` header and back off; read `x-ratelimit-remaining-requests`.
- **Errors:** OpenAI-style error object `{ error: { message, type, param, code,
  doc_url } }`. `401 authentication_error` = bad key; `402 payment_required` =
  account credit exhausted (top up in the dashboard). See
  errors/flexai-problem-types.yml.

See conventions/flexai-conventions.yml for the full request/response contract.
