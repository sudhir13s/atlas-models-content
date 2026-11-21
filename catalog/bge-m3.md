---
id: model/bge-m3
type: model
slug: bge-m3
schema_version: 1.1
title: bge-m3
organization: organization/baai
release_date: '2024-01-27'
release_year: 2024
release_month: 1
license: license/mit
model_category: retrieval
tasks:
- task/embedding-models
context_window_tokens: 8194
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  intermediate_size: 4096
  context_window_tokens: 8194
  vocabulary_size: 250002
  activation_function: gelu
  normalization: LayerNorm
  positional_encoding: Learned
filters:
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: sentence-similarity
  library: sentence-transformers
hub_signals:
  downloads_30d: 33121367
  likes: 4028
  last_modified: '2024-07-03'
  observed_at: '2026-11-21'
  source: https://huggingface.co/BAAI/bge-m3
aliases:
- BAAI/bge-m3
lineage:
  parents: []
  children: []
tags:
- sentence-similarity
- xlm-roberta
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/BAAI/bge-m3
  title: BAAI/bge-m3 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/BAAI/bge-m3/blob/main/config.json
  title: BAAI/bge-m3 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2402.03216
  title: arXiv:2402.03216
  trust: 0.95
- url: https://arxiv.org/abs/2004.04906
  title: arXiv:2004.04906
  trust: 0.95
- url: https://arxiv.org/abs/2106.14807
  title: arXiv:2106.14807
  trust: 0.95
- url: https://arxiv.org/abs/2107.05720
  title: arXiv:2107.05720
  trust: 0.95
- url: https://arxiv.org/abs/2004.12832
  title: arXiv:2004.12832
  trust: 0.95
---


