---
id: model/mistral-7b-instruct-v0-2
type: model
slug: mistral-7b-instruct-v0-2
schema_version: 1.1
title: Mistral-7B-Instruct-v0.2
organization: organization/mistral-ai
release_date: '2023-12-11'
release_year: 2023
release_month: 12
license: license/apache-2-0
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
  downloads_30d: 1071464
  likes: 3283
  last_modified: '2025-07-24'
  observed_at: '2026-11-19'
  source: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2
aliases:
- mistralai/Mistral-7B-Instruct-v0.2
- mistral-7b-instruct-v0.2
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- mistral
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2
  title: mistralai/Mistral-7B-Instruct-v0.2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2/blob/main/config.json
  title: mistralai/Mistral-7B-Instruct-v0.2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2310.06825
  title: arXiv:2310.06825
  trust: 0.95
---


