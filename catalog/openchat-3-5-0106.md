---
id: model/openchat-3-5-0106
type: model
slug: openchat-3-5-0106
schema_version: 1.1
title: openchat-3.5-0106
organization: organization/openchat
release_date: '2024-01-07'
release_year: 2024
release_month: 1
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.24
total_parameters: 7241748480
context_window_tokens: 8192
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 32
  kv_heads: 8
  head_size: 128
  hidden_size: 4096
  intermediate_size: 14336
  context_window_tokens: 8192
  vocabulary_size: 32002
  activation_function: silu
  normalization: RMSNorm
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
  downloads_30d: 12379
  likes: 357
  last_modified: '2024-05-18'
  observed_at: '2026-11-19'
  source: https://huggingface.co/openchat/openchat-3.5-0106
aliases:
- openchat/openchat-3.5-0106
- openchat-3.5-0106
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
updated: '2026-11-19'
sources:
- url: https://huggingface.co/openchat/openchat-3.5-0106
  title: openchat/openchat-3.5-0106 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openchat/openchat-3.5-0106/blob/main/config.json
  title: openchat/openchat-3.5-0106 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2309.11235
  title: arXiv:2309.11235
  trust: 0.95
- url: https://arxiv.org/abs/2303.08774
  title: arXiv:2303.08774
  trust: 0.95
---


