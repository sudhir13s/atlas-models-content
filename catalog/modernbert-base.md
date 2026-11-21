---
id: model/modernbert-base
type: model
slug: modernbert-base
schema_version: 1.1
title: ModernBERT-base
organization: organization/answerdotai
release_date: '2024-12-11'
release_year: 2024
release_month: 12
license: license/apache-2-0
model_category: language
tasks:
- task/fill-mask
parameters_active_b: 0.15
total_parameters: 149655232
context_window_tokens: 8192
fingerprint:
  attention_type: MHA
  num_layers: 22
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 1152
  context_window_tokens: 8192
  vocabulary_size: 50368
  normalization: LayerNorm
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: fill-mask
  library: transformers
hub_signals:
  downloads_30d: 2780362
  likes: 1159
  last_modified: '2025-01-15'
  observed_at: '2026-11-21'
  source: https://huggingface.co/answerdotai/ModernBERT-base
aliases:
- answerdotai/ModernBERT-base
lineage:
  parents: []
  children: []
tags:
- llm
- fill-mask
- modernbert
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/answerdotai/ModernBERT-base
  title: answerdotai/ModernBERT-base — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/answerdotai/ModernBERT-base/blob/main/config.json
  title: answerdotai/ModernBERT-base — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2412.13663
  title: arXiv:2412.13663
  trust: 0.95
---


