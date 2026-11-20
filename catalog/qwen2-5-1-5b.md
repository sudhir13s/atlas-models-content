---
id: model/qwen2-5-1-5b
type: model
slug: qwen2-5-1-5b
schema_version: 1.1
title: Qwen2.5-1.5B
organization: organization/qwen
release_date: '2024-09-15'
release_year: 2024
release_month: 9
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 1.54
total_parameters: 1543714304
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 12
  kv_heads: 2
  head_size: 128
  hidden_size: 1536
  intermediate_size: 8960
  context_window_tokens: 131072
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 497363
  likes: 249
  last_modified: '2024-10-08'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-1.5B
variants:
- name: RedHatAI/Qwen2.5-1.5B-quantized.w8a8
  format: w8a8
  address: https://huggingface.co/RedHatAI/Qwen2.5-1.5B-quantized.w8a8
  publisher: RedHatAI
aliases:
- Qwen/Qwen2.5-1.5B
- qwen2.5-1.5b-quantized.w8a8
lineage:
  parents: []
  children:
  - model/qwen2-5-1-5b-instruct
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-20'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-1.5B
  title: Qwen/Qwen2.5-1.5B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-1.5B/blob/main/config.json
  title: Qwen/Qwen2.5-1.5B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
---


