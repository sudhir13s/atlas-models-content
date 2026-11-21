---
id: model/xclip-base-patch32
type: model
slug: xclip-base-patch32
schema_version: 1.1
title: xclip-base-patch32
organization: organization/microsoft
release_date: '2022-08-25'
release_year: 2022
release_month: 8
license: license/mit
model_category: vision
tasks:
- task/video-understanding
parameters_active_b: 0.2
total_parameters: 196585855
context_window_tokens: 77
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 8
  kv_heads: 8
  head_size: 64
  hidden_size: 512
  intermediate_size: 2048
  context_window_tokens: 77
  vocabulary_size: 49408
  activation_function: quick_gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: video-classification
  library: transformers
hub_signals:
  downloads_30d: 61365
  likes: 126
  last_modified: '2024-02-04'
  observed_at: '2026-11-21'
  source: https://huggingface.co/microsoft/xclip-base-patch32
aliases:
- microsoft/xclip-base-patch32
lineage:
  parents: []
  children: []
tags:
- video-classification
- xclip
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/microsoft/xclip-base-patch32
  title: microsoft/xclip-base-patch32 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/xclip-base-patch32/blob/main/config.json
  title: microsoft/xclip-base-patch32 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2208.02816
  title: arXiv:2208.02816
  trust: 0.95
---


