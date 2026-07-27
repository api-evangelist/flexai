---
name: Generate embeddings with FlexAI
description: >-
  Use the FlexAI Token Factory OpenAI-compatible API to generate vector
  embeddings from open embedding models for search, RAG, and clustering.
api: openapi/flexai-token-factory-openapi.yml
operations: [listModels, createEmbedding]
---

# Embeddings with FlexAI Token Factory

## Prerequisites
- FlexAI API key (https://tokens.flex.ai/signup).
- Base URL: `https://tokens.flex.ai/v1`; header `Authorization: Bearer $FLEXAI_API_KEY`.

## Steps

1. **Pick an embedding model** — call `listModels` (`GET /v1/models`) and choose
   a model annotated as an embedding model (e.g. a BGE-M3 variant).

2. **Create embeddings** — call `createEmbedding` (`POST /v1/embeddings`) with
   `model` and `input` (a string or an array of strings). Set
   `encoding_format` to `float` (default) or `base64`.

3. **Use the vectors** — store the returned vectors in your vector database for
   similarity search / RAG.

## Rules and gotchas
- **Billing:** embeddings are billed per input token (per million tokens).
- **Batching:** pass an array of strings as `input` to embed many texts in one
  call and reduce request count against the rate limit.
- **Rate limits:** 10 rpm (free) / 100 rpm (paid) per key; on `429` honor
  `Retry-After`.
- **Errors:** OpenAI-style error object; `400 invalid_request_error` names the
  bad field in `param`. See errors/flexai-problem-types.yml.

See conventions/flexai-conventions.yml for the full request/response contract.
