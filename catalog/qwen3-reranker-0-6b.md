---
id: model/qwen3-reranker-0-6b
type: model
slug: qwen3-reranker-0-6b
schema_version: 1.1
title: Qwen3-Reranker-0.6B
organization: organization/qwen
release_date: '2025-05-29'
release_year: 2025
release_month: 5
license: license/apache-2-0
model_category: retrieval
tasks:
- task/reranking-models
parameters_active_b: 0.6
total_parameters: 595776512
context_window_tokens: 40960
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 1024
  intermediate_size: 3072
  context_window_tokens: 40960
  vocabulary_size: 151669
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-ranking
  library: transformers
hub_signals:
  downloads_30d: 1182124
  likes: 441
  last_modified: '2026-04-16'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen3-Reranker-0.6B
aliases:
- Qwen/Qwen3-Reranker-0.6B
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
- url: https://huggingface.co/Qwen/Qwen3-Reranker-0.6B
  title: Qwen/Qwen3-Reranker-0.6B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-Reranker-0.6B/blob/main/config.json
  title: Qwen/Qwen3-Reranker-0.6B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2506.05176
  title: arXiv:2506.05176
  trust: 0.95
---


