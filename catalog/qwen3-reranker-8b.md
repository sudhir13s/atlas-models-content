---
id: model/qwen3-reranker-8b
type: model
slug: qwen3-reranker-8b
schema_version: 1.1
title: Qwen3-Reranker-8B
organization: organization/qwen
release_date: '2025-05-29'
release_year: 2025
release_month: 5
license: license/apache-2-0
model_category: retrieval
tasks:
- task/reranking-models
parameters_active_b: 8.19
total_parameters: 8188548096
context_window_tokens: 40960
fingerprint:
  attention_type: GQA
  num_layers: 36
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 12288
  context_window_tokens: 40960
  vocabulary_size: 151669
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-ranking
  library: transformers
hub_signals:
  downloads_30d: 60844
  likes: 298
  last_modified: '2026-04-16'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen3-Reranker-8B
aliases:
- Qwen/Qwen3-Reranker-8B
lineage:
  parents: []
  children: []
tags:
- text-ranking
- qwen3
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/Qwen/Qwen3-Reranker-8B
  title: Qwen/Qwen3-Reranker-8B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-Reranker-8B/blob/main/config.json
  title: Qwen/Qwen3-Reranker-8B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2506.05176
  title: arXiv:2506.05176
  trust: 0.95
---


