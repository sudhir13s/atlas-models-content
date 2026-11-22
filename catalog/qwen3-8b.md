---
id: model/qwen3-8b
type: model
slug: qwen3-8b
schema_version: 1.1
title: Qwen3-8B
organization: organization/qwen
release_date: '2025-04-27'
release_year: 2025
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
- task/tool-calling-models
parameters_active_b: 8.19
total_parameters: 8190735360
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
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
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
  downloads_30d: 6141295
  likes: 2204
  last_modified: '2025-07-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-8B
variants:
- name: Qwen/Qwen3-8B-AWQ
  format: awq
  address: https://huggingface.co/Qwen/Qwen3-8B-AWQ
  publisher: Qwen
- name: unsloth/Qwen3-8B-unsloth-bnb-4bit
  format: bnb-4bit
  address: https://huggingface.co/unsloth/Qwen3-8B-unsloth-bnb-4bit
  publisher: unsloth
aliases:
- Qwen/Qwen3-8B
- qwen3-8b-awq
- qwen3-8b-unsloth-bnb-4bit
lineage:
  parents:
  - model/qwen3-8b-base
  children: []
tags:
- llm
- text-generation
- qwen3
- dense
- gqa
updated: '2026-11-22'
sources:
- url: https://huggingface.co/Qwen/Qwen3-8B
  title: Qwen/Qwen3-8B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen/Qwen3-8B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.00071
  title: arXiv:2309.00071
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


