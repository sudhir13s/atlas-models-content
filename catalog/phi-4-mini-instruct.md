---
id: model/phi-4-mini-instruct
type: model
slug: phi-4-mini-instruct
schema_version: 1.1
title: Phi-4-mini-instruct
organization: organization/microsoft
release_date: '2025-02-19'
release_year: 2025
release_month: 2
license: license/mit
model_category: language
tasks:
- task/text-generation
parameters_active_b: 3.84
total_parameters: 3836021760
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 24
  kv_heads: 8
  head_size: 128
  hidden_size: 3072
  intermediate_size: 8192
  context_window_tokens: 131072
  vocabulary_size: 200064
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 301098
  likes: 891
  last_modified: '2025-12-10'
  observed_at: '2026-11-19'
  source: https://huggingface.co/microsoft/Phi-4-mini-instruct
aliases:
- microsoft/Phi-4-mini-instruct
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- phi3
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/microsoft/Phi-4-mini-instruct
  title: microsoft/Phi-4-mini-instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/Phi-4-mini-instruct/blob/main/config.json
  title: microsoft/Phi-4-mini-instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2503.01743
  title: arXiv:2503.01743
  trust: 0.95
---


