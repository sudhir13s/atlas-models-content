---
id: model/privacy-filter
type: model
slug: privacy-filter
schema_version: 1.1
title: privacy-filter
organization: organization/openai
release_date: '2026-04-17'
release_year: 2026
release_month: 4
license: license/apache-2-0
model_category: language
tasks:
- task/token-classification
total_parameters: 1399486865
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 8
  attention_heads: 14
  kv_heads: 2
  head_size: 64
  hidden_size: 640
  intermediate_size: 640
  context_window_tokens: 131072
  vocabulary_size: 200064
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: encoder
  positional_encoding: RoPE
  rope_theta: 150000.0
moe_config:
  expert_count: 128
  active_experts: 4
filters:
  size_category: small
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: token-classification
  library: transformers
hub_signals:
  downloads_30d: 114097
  likes: 1841
  last_modified: '2026-04-22'
  observed_at: '2026-11-21'
  source: https://huggingface.co/openai/privacy-filter
aliases:
- openai/privacy-filter
lineage:
  parents: []
  children: []
tags:
- llm
- token-classification
- openai-privacy-filter
- moe
- gqa
updated: '2026-11-22'
sources:
- url: https://huggingface.co/openai/privacy-filter
  title: openai/privacy-filter — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/privacy-filter/blob/main/config.json
  title: openai/privacy-filter — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/openai_privacy_filter/modeling_openai_privacy_filter.py
  title: 'transformers/models/openai_privacy_filter/modeling_openai_privacy_filter.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


