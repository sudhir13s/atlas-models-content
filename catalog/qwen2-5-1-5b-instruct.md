---
id: model/qwen2-5-1-5b-instruct
type: model
slug: qwen2-5-1-5b-instruct
schema_version: 1.1
title: Qwen2.5-1.5B-Instruct
organization: organization/qwen
release_date: '2024-09-17'
release_year: 2024
release_month: 9
license: license/apache-2-0
model_category: language
parameters_active_b: 1.54
total_parameters: 1543714304
context_window_tokens: 32768
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 12
  kv_heads: 2
  head_size: 128
  hidden_size: 1536
  intermediate_size: 8960
  context_window_tokens: 32768
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
  downloads_30d: 4991225
  likes: 947
  last_modified: '2024-09-25'
  observed_at: '2026-11-19'
  source: https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct
variants:
- name: Qwen/Qwen2.5-1.5B-Instruct-AWQ
  format: awq
  address: https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct-AWQ
  publisher: Qwen
aliases:
- Qwen/Qwen2.5-1.5B-Instruct
- qwen2.5-1.5b-instruct
- qwen2.5-1.5b-instruct-awq
lineage:
  parents:
  - model/qwen2-5-1-5b
  children: []
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct
  title: Qwen/Qwen2.5-1.5B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-1.5B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
---


