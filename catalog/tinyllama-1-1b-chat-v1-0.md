---
id: model/tinyllama-1-1b-chat-v1-0
type: model
slug: tinyllama-1-1b-chat-v1-0
schema_version: 1.1
title: TinyLlama-1.1B-Chat-v1.0
organization: organization/tinyllama
release_date: '2023-12-30'
release_year: 2023
release_month: 12
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 1.1
total_parameters: 1100048384
context_window_tokens: 2048
fingerprint:
  attention_type: GQA
  num_layers: 22
  attention_heads: 32
  kv_heads: 4
  head_size: 64
  hidden_size: 2048
  intermediate_size: 5632
  context_window_tokens: 2048
  vocabulary_size: 32000
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
  downloads_30d: 981187
  likes: 1917
  last_modified: '2024-03-17'
  observed_at: '2026-11-19'
  source: https://huggingface.co/TinyLlama/TinyLlama-1.1B-Chat-v1.0
aliases:
- TinyLlama/TinyLlama-1.1B-Chat-v1.0
- tinyllama-1.1b-chat-v1.0
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- llama
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/TinyLlama/TinyLlama-1.1B-Chat-v1.0
  title: TinyLlama/TinyLlama-1.1B-Chat-v1.0 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/TinyLlama/TinyLlama-1.1B-Chat-v1.0/blob/main/config.json
  title: TinyLlama/TinyLlama-1.1B-Chat-v1.0 — config.json
  trust: 0.9
---


