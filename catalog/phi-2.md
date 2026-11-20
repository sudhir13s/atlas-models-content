---
id: model/phi-2
type: model
slug: phi-2
schema_version: 1.1
title: phi-2
organization: organization/microsoft
release_date: '2023-12-13'
release_year: 2023
release_month: 12
license: license/mit
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 2.78
total_parameters: 2779683840
context_window_tokens: 2048
fingerprint:
  attention_type: MHA
  num_layers: 32
  attention_heads: 32
  kv_heads: 32
  head_size: 80
  hidden_size: 2560
  intermediate_size: 10240
  context_window_tokens: 2048
  vocabulary_size: 51200
  activation_function: gelu_new
  normalization: LayerNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 185045
  likes: 3538
  last_modified: '2025-12-08'
  observed_at: '2026-11-20'
  source: https://huggingface.co/microsoft/phi-2
aliases:
- microsoft/phi-2
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- phi
- dense
- mha
updated: '2026-11-20'
sources:
- url: https://huggingface.co/microsoft/phi-2
  title: microsoft/phi-2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/phi-2/blob/main/config.json
  title: microsoft/phi-2 — config.json
  trust: 0.9
---


