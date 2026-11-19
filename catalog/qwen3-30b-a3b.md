---
id: model/qwen3-30b-a3b
type: model
slug: qwen3-30b-a3b
schema_version: 1.1
title: Qwen3-30B-A3B
organization: organization/qwen
release_date: '2025-04-27'
release_year: 2025
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 30532122624
context_window_tokens: 40960
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 32
  kv_heads: 4
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  context_window_tokens: 40960
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
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
  downloads_30d: 1120507
  likes: 986
  last_modified: '2025-07-26'
  observed_at: '2026-11-19'
  source: https://huggingface.co/Qwen/Qwen3-30B-A3B
aliases:
- Qwen/Qwen3-30B-A3B
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
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B
  title: Qwen/Qwen3-30B-A3B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json
  title: Qwen/Qwen3-30B-A3B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
---


