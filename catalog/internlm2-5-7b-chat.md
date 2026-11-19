---
id: model/internlm2-5-7b-chat
type: model
slug: internlm2-5-7b-chat
schema_version: 1.1
title: internlm2_5-7b-chat
organization: organization/internlm
release_date: '2024-06-27'
release_year: 2024
release_month: 6
license_note: other
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.74
total_parameters: 7737708544
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
  vocabulary_size: 92544
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
  downloads_30d: 20780
  likes: 202
  last_modified: '2025-03-13'
  observed_at: '2026-11-19'
  source: https://huggingface.co/internlm/internlm2_5-7b-chat
aliases:
- internlm/internlm2_5-7b-chat
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- internlm2
- dense
- gqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/internlm/internlm2_5-7b-chat
  title: internlm/internlm2_5-7b-chat — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/internlm/internlm2_5-7b-chat/blob/main/config.json
  title: internlm/internlm2_5-7b-chat — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2403.17297
  title: arXiv:2403.17297
  trust: 0.95
---


