---
id: model/qwen2-5-0-5b-instruct
type: model
slug: qwen2-5-0-5b-instruct
schema_version: 1.1
title: Qwen2.5-0.5B-Instruct
organization: organization/qwen
release_date: '2024-09-16'
release_year: 2024
release_month: 9
license: license/apache-2-0
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.49
total_parameters: 494032768
context_window_tokens: 32768
fingerprint:
  attention_type: GQA
  num_layers: 24
  attention_heads: 14
  kv_heads: 2
  head_size: 64
  hidden_size: 896
  intermediate_size: 4864
  context_window_tokens: 32768
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
  downloads_30d: 4041057
  likes: 756
  last_modified: '2024-09-25'
  observed_at: '2026-11-20'
  source: https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct
aliases:
- Qwen/Qwen2.5-0.5B-Instruct
- qwen2.5-0.5b-instruct
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- qwen2
- dense
- gqa
updated: '2026-11-22'
sources:
- url: https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct
  title: Qwen/Qwen2.5-0.5B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct/blob/main/config.json
  title: Qwen/Qwen2.5-0.5B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2407.10671
  title: arXiv:2407.10671
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen2/modeling_qwen2.py
  title: 'transformers/models/qwen2/modeling_qwen2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


