---
id: model/qwen3-8-27b
type: model
slug: qwen3-8-27b
schema_version: 1.1
title: Qwen3.8-27B
organization: organization/qwen
release_date: '2026-08-05'
release_year: 2026
release_month: 8
license: license/apache-2-0
model_category: language
tasks:
- task/chat-models
- task/vision-language-models
parameters_active_b: 27.78
total_parameters: 27781427952
context_window_tokens: 262144
fingerprint:
  attention_type: GQA
  num_layers: 64
  attention_heads: 24
  kv_heads: 4
  head_size: 256
  hidden_size: 5120
  intermediate_size: 17408
  context_window_tokens: 262144
  vocabulary_size: 248320
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000000.0
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 6454419
  likes: 20815
  last_modified: '2026-11-07'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen3.8-27B
aliases:
- Qwen/Qwen3.8-27B
lineage:
  parents: []
  children: []
tags:
- llm
- image-text-to-text
- qwen3-5
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/Qwen/Qwen3.8-27B
  title: Qwen/Qwen3.8-27B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3.8-27B/blob/main/config.json
  title: Qwen/Qwen3.8-27B — config.json
  trust: 0.9
---


