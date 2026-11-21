---
id: model/piiranha-v1-detect-personal-information
type: model
slug: piiranha-v1-detect-personal-information
schema_version: 1.1
title: piiranha-v1-detect-personal-information
organization: organization/iiiorg
release_date: '2024-09-12'
release_year: 2024
release_month: 9
license_note: cc-by-nc-nd-4.0
model_category: language
tasks:
- task/token-classification
parameters_active_b: 0.28
total_parameters: 278232594
context_window_tokens: 512
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 512
  vocabulary_size: 251000
  activation_function: gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: token-classification
  library: transformers
hub_signals:
  downloads_30d: 202621
  likes: 250
  last_modified: '2025-11-01'
  observed_at: '2026-11-21'
  source: https://huggingface.co/iiiorg/piiranha-v1-detect-personal-information
aliases:
- iiiorg/piiranha-v1-detect-personal-information
lineage:
  parents: []
  children: []
tags:
- llm
- token-classification
- deberta-v2
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/iiiorg/piiranha-v1-detect-personal-information
  title: iiiorg/piiranha-v1-detect-personal-information — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/iiiorg/piiranha-v1-detect-personal-information/blob/main/config.json
  title: iiiorg/piiranha-v1-detect-personal-information — config.json
  trust: 0.9
---


