---
id: model/qwen3-8-flash-next
type: model
slug: qwen3-8-flash-next
schema_version: 1.1
title: Qwen3.8-Flash-Next
organization: organization/qwen
release_date: '2026-08-24'
release_year: 2026
release_month: 8
license_note: qwen-community-1.0
model_category: language
tasks:
- task/chat-models
- task/vision-language-models
total_parameters: 179999981459
context_window_tokens: 262144
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 24
  kv_heads: 2
  head_size: 256
  hidden_size: 2560
  context_window_tokens: 262144
  vocabulary_size: 248320
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000000.0
moe_config:
  expert_count: 512
  active_experts: 10
  shared_expert: true
filters:
  size_category: xlarge
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 2727999
  likes: 7522
  last_modified: '2026-11-07'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen3.8-Flash-Next
aliases:
- Qwen/Qwen3.8-Flash-Next
lineage:
  parents: []
  children: []
tags:
- llm
- image-text-to-text
- qwen4-exp
- moe
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/Qwen/Qwen3.8-Flash-Next
  title: Qwen/Qwen3.8-Flash-Next — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/main/config.json
  title: Qwen/Qwen3.8-Flash-Next — config.json
  trust: 0.9
---


