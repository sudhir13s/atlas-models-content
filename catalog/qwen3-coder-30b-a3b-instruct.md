---
id: model/qwen3-coder-30b-a3b-instruct
type: model
slug: qwen3-coder-30b-a3b-instruct
schema_version: 1.1
title: Qwen3-Coder-30B-A3B-Instruct
organization: organization/qwen
release_date: '2025-07-31'
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
  downloads_30d: 1940236
  likes: 1345
  last_modified: '2025-12-03'
  observed_at: '2026-11-19'
  source: https://huggingface.co/Qwen/Qwen3-Coder-30B-A3B-Instruct
aliases:
- Qwen/Qwen3-Coder-30B-A3B-Instruct
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
- url: https://huggingface.co/Qwen/Qwen3-Coder-30B-A3B-Instruct
  title: Qwen/Qwen3-Coder-30B-A3B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-Coder-30B-A3B-Instruct/blob/main/config.json
  title: Qwen/Qwen3-Coder-30B-A3B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
---


