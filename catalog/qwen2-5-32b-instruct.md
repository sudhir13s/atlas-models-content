---
id: model/qwen2-5-32b-instruct
type: model
slug: qwen2-5-32b-instruct
schema_version: 1.1
title: Qwen2.5-32B-Instruct
organization: organization/qwen
release_date: '2024-09-17'
release_year: 2024
release_month: 9
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 32.76
total_parameters: 32763876352
context_window_tokens: 32768
fingerprint:
  attention_type: GQA
  num_layers: 64
  attention_heads: 40
  kv_heads: 8
  head_size: 128
  hidden_size: 5120
  intermediate_size: 27648
  context_window_tokens: 32768
  vocabulary_size: 152064
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 664543
  likes: 378
  last_modified: '2024-09-25'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-32B-Instruct
variants:
- name: Qwen/Qwen2.5-32B-Instruct-AWQ
  format: awq
  address: https://huggingface.co/Qwen/Qwen2.5-32B-Instruct-AWQ
  publisher: Qwen
aliases:
- Qwen/Qwen2.5-32B-Instruct
- qwen2.5-32b-instruct
- qwen2.5-32b-instruct-awq
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-20'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-32B-Instruct
  title: Qwen/Qwen2.5-32B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-32B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-32B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
---


