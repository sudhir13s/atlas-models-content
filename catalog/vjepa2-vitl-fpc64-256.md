---
id: model/vjepa2-vitl-fpc64-256
type: model
slug: vjepa2-vitl-fpc64-256
schema_version: 1.1
title: vjepa2-vitl-fpc64-256
organization: organization/meta
release_date: '2025-05-31'
release_year: 2025
release_month: 5
license: license/mit
model_category: vision
tasks:
- task/video-understanding
parameters_active_b: 0.33
total_parameters: 325971328
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  activation_function: gelu
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
  downloads_30d: 370472
  likes: 222
  last_modified: '2025-08-11'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/vjepa2-vitl-fpc64-256
aliases:
- facebook/vjepa2-vitl-fpc64-256
lineage:
  parents: []
  children: []
tags:
- video-classification
- vjepa2
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/facebook/vjepa2-vitl-fpc64-256
  title: facebook/vjepa2-vitl-fpc64-256 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/vjepa2-vitl-fpc64-256/blob/main/config.json
  title: facebook/vjepa2-vitl-fpc64-256 — config.json
  trust: 0.9
---


