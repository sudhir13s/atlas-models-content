---
id: model/zephyr-7b-beta
type: model
slug: zephyr-7b-beta
schema_version: 1.1
title: zephyr-7b-beta
organization: organization/hugging-face
release_date: '2023-10-26'
release_year: 2023
release_month: 10
license: license/mit
architecture_id: architecture-era/llama-recipe
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.24
total_parameters: 7241732096
context_window_tokens: 32768
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  context_window_tokens: 32768
  vocabulary_size: 32000
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 46472
  likes: 1868
  last_modified: '2024-10-16'
  observed_at: '2026-11-20'
  source: https://huggingface.co/HuggingFaceH4/zephyr-7b-beta
aliases:
- HuggingFaceH4/zephyr-7b-beta
lineage:
  parents:
  - model/mistral-7b-v0-1
  children: []
tags:
- llm
- text-generation
- mistral
- dense
- gqa
updated: '2026-11-22'
sources:
- url: https://huggingface.co/HuggingFaceH4/zephyr-7b-beta
  title: HuggingFaceH4/zephyr-7b-beta — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/HuggingFaceH4/zephyr-7b-beta/blob/main/config.json
  title: HuggingFaceH4/zephyr-7b-beta — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2305.18290
  title: arXiv:2305.18290
  trust: 0.95
- url: https://arxiv.org/abs/2310.16944
  title: arXiv:2310.16944
  trust: 0.95
- url: https://arxiv.org/abs/2305.14233
  title: arXiv:2305.14233
  trust: 0.95
- url: https://arxiv.org/abs/2310.01377
  title: arXiv:2310.01377
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/mistral/modeling_mistral.py
  title: 'transformers/models/mistral/modeling_mistral.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


