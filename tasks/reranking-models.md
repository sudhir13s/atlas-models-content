---
id: task/reranking-models
type: task
slug: reranking-models
schema_version: 3.1
title: Reranking Models
group: task-group/retrieval
order: 2
summary: Score how well each candidate passage answers a query so the best ones can be moved to the top.
hf_pipeline_tags:
- text-ranking
inputs:
- text
outputs:
- score
tags:
- reranking
- retrieval
- cross-encoder
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/text-ranking
  title: Hugging Face — Text Ranking task
  trust: 0.9
---

## What It Is

A reranking model reads a query and a passage **together** and returns one relevance score for the pair.

- **Cross-encoders** are the usual design: both texts pass through the model at once, so it sees how they relate.
- **Second-stage use:** a faster search finds a shortlist first, and the reranker reorders only that shortlist.
- **No stored vectors:** scores are computed at query time and cannot be indexed in advance.

## When To Use

- **Use it** when a first search returns the right passages but in the wrong order.
- **Use it** to trim the context sent to a generator in retrieval-augmented generation (RAG).
- **Use an embedding model instead** to search a whole collection; scoring every pair is too slow at that scale.
- **Use text classification instead** when each text needs a fixed label rather than a rank against a query.

## How To Choose

- **Latency per pair:** total delay grows with the number of candidates you rerank.
- **Maximum input length:** the query and passage share one limit, so long passages get cut.
- **Languages and domain:** check coverage of the languages and subject matter you search.
- **Licence:** open weights do not always allow commercial use.
