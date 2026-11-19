---
id: model/qwen3-30b-a3b-instruct-2507
type: model
slug: qwen3-30b-a3b-instruct-2507
schema_version: 1.1
title: Qwen3-30B-A3B-Instruct-2507
organization: organization/qwen
release_date: '2025-07-28'
release_year: 2025
release_month: 7
license: license/apache-2-0
model_category: language
total_parameters: 30532122624
context_window_tokens: 262144
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 32
  kv_heads: 4
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  context_window_tokens: 262144
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000000.0
moe_config:
  expert_count: 128
  active_experts: 8
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 747351
  likes: 856
  last_modified: '2025-09-17'
  observed_at: '2026-11-19'
  source: https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507
aliases:
- Qwen/Qwen3-30B-A3B-Instruct-2507
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen3-moe
- moe
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507
  title: Qwen/Qwen3-30B-A3B-Instruct-2507 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507/blob/main/config.json
  title: Qwen/Qwen3-30B-A3B-Instruct-2507 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2402.17463
  title: arXiv:2402.17463
  trust: 0.95
- url: https://arxiv.org/abs/2407.02490
  title: arXiv:2407.02490
  trust: 0.95
- url: https://arxiv.org/abs/2501.15383
  title: arXiv:2501.15383
  trust: 0.95
- url: https://arxiv.org/abs/2404.06654
  title: arXiv:2404.06654
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
---


