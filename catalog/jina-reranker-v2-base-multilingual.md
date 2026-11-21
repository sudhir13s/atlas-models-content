---
id: model/jina-reranker-v2-base-multilingual
type: model
slug: jina-reranker-v2-base-multilingual
schema_version: 1.1
title: jina-reranker-v2-base-multilingual
organization: organization/jinaai
release_date: '2024-06-19'
release_year: 2024
release_month: 6
license_note: cc-by-nc-4.0
model_category: retrieval
tasks:
- task/reranking-models
parameters_active_b: 0.28
total_parameters: 278437633
context_window_tokens: 1026
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 1026
  vocabulary_size: 250002
  activation_function: gelu
  normalization: LayerNorm
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-ranking
  library: transformers
hub_signals:
  downloads_30d: 970236
  likes: 372
  last_modified: '2025-10-21'
  observed_at: '2026-11-21'
  source: https://huggingface.co/jinaai/jina-reranker-v2-base-multilingual
aliases:
- jinaai/jina-reranker-v2-base-multilingual
lineage:
  parents: []
  children: []
tags:
- text-ranking
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/jinaai/jina-reranker-v2-base-multilingual
  title: jinaai/jina-reranker-v2-base-multilingual — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/jinaai/jina-reranker-v2-base-multilingual/blob/main/config.json
  title: jinaai/jina-reranker-v2-base-multilingual — config.json
  trust: 0.9
---


