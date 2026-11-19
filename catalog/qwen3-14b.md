---
id: model/qwen3-14b
type: model
slug: qwen3-14b
schema_version: 1.1
title: Qwen3-14B
organization: organization/qwen
release_date: '2025-04-27'
release_year: 2025
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 14.77
total_parameters: 14768307200
context_window_tokens: 40960
fingerprint:
  attention_type: GQA
  num_layers: 40
  attention_heads: 40
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 17408
  context_window_tokens: 40960
  vocabulary_size: 151936
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
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 13223290
  likes: 549
  last_modified: '2025-07-26'
  observed_at: '2026-11-19'
  source: https://huggingface.co/Qwen/Qwen3-14B
variants:
- name: Qwen/Qwen3-14B-AWQ
  format: awq
  address: https://huggingface.co/Qwen/Qwen3-14B-AWQ
  publisher: Qwen
aliases:
- Qwen/Qwen3-14B
- qwen3-14b-awq
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen3
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/Qwen/Qwen3-14B
  title: Qwen/Qwen3-14B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-14B/blob/main/config.json
  title: Qwen/Qwen3-14B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
---


