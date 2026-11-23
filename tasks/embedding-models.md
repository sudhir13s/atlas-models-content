---
id: task/embedding-models
type: task
slug: embedding-models
schema_version: 3.1
title: Embedding Models
group: task-group/retrieval
order: 1
summary: Turn a piece of text into a vector so that similar meanings land close together.
hf_pipeline_tags:
- feature-extraction
- sentence-similarity
inputs:
- text
outputs:
- embedding
benchmarks:
- benchmark/mteb-eng-v2
- benchmark/mteb-multilingual-v2
- benchmark/longembed
tags:
- embeddings
- retrieval
- semantic-search
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/feature-extraction
  title: Hugging Face — Feature Extraction task
  trust: 0.9
---

## What It Is

An embedding model converts text into **numbers that capture its meaning**, so texts can be compared by distance.

- **Dense embeddings** give one fixed-length vector per text and match on meaning rather than exact words.
- **Sparse embeddings** give a weight per vocabulary term and keep the strengths of keyword search.
- **Late-interaction embeddings** keep one vector per token and compare query and passage token by token.

## When To Use

- **Use it** for semantic search, clustering, deduplication and the retrieval step of retrieval-augmented generation (RAG).
- **Use a reranking model as well** when the first results must be ordered more precisely.
- **Use image and multimodal embeddings instead** when the items to search are pictures or page images.
- **Use text generation instead** when the answer must be written rather than looked up.

## How To Choose

- **Vector size:** larger vectors can hold more detail but cost more storage and slower search.
- **Maximum input length:** text beyond the limit is cut off, so it decides how you split documents.
- **Languages and domain:** a model trained on general English may rank poorly on other languages or specialist text.
- **Embedding type:** dense, sparse or late-interaction must match what your vector store can index.
