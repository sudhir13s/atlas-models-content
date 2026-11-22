---
id: model/qwen3-0-6b
type: model
slug: qwen3-0-6b
schema_version: 1.1
title: Qwen3-0.6B
organization: organization/qwen
release_date: '2025-04-27'
release_year: 2025
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.75
total_parameters: 751632384
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
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 15392833
  likes: 1920
  last_modified: '2025-07-26'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen3-0.6B
variants:
- name: Qwen/Qwen3-0.6B-FP8
  format: fp8
  address: https://huggingface.co/Qwen/Qwen3-0.6B-FP8
  publisher: Qwen
aliases:
- Qwen/Qwen3-0.6B
- qwen3-0.6b
- qwen3-0.6b-fp8
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen3
- dense
- gqa
updated: '2026-11-22'
sources:
- url: https://huggingface.co/Qwen/Qwen3-0.6B
  title: Qwen/Qwen3-0.6B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-0.6B/blob/main/config.json
  title: Qwen/Qwen3-0.6B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: arXiv:2505.09388
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'transformers/models/qwen3/modeling_qwen3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


