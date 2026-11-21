---
id: model/embeddinggemma-2
type: model
slug: embeddinggemma-2
schema_version: 1.1
title: embeddinggemma-2
organization: organization/google-deepmind
release_date: '2026-09-14'
release_year: 2026
release_month: 9
license: license/apache-2-0
model_category: retrieval
tasks:
- task/embedding-models
parameters_active_b: 0.74
total_parameters: 744371512
context_window_tokens: 262144
fingerprint:
  attention_type: GQA
  num_layers: 24
  attention_heads: 4
  kv_heads: 2
  head_size: 256
  hidden_size: 512
  intermediate_size: 2048
  context_window_tokens: 262144
  vocabulary_size: 262144
  normalization: RMSNorm
  positional_encoding: RoPE
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: feature-extraction
  library: transformers
hub_signals:
  downloads_30d: 612777
  likes: 3561
  last_modified: '2026-10-28'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/embeddinggemma-2
aliases:
- google/embeddinggemma-2
lineage:
  parents: []
  children: []
tags:
- feature-extraction
- embedding-gemma2
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/google/embeddinggemma-2
  title: google/embeddinggemma-2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/embeddinggemma-2/blob/main/config.json
  title: google/embeddinggemma-2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2610.37364
  title: arXiv:2610.37364
  trust: 0.95
---


