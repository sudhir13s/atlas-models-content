---
id: model/siglip-so400m-patch14-384
type: model
slug: siglip-so400m-patch14-384
schema_version: 1.1
title: siglip-so400m-patch14-384
organization: organization/google-deepmind
release_date: '2024-01-08'
release_year: 2024
release_month: 1
license: license/apache-2-0
model_category: vision
tasks:
- task/image-classification
parameters_active_b: 0.88
total_parameters: 877960498
fingerprint:
  attention_type: MHA
  num_layers: 27
  attention_heads: 16
  kv_heads: 16
  head_size: 72
  hidden_size: 1152
  intermediate_size: 4304
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: zero-shot-image-classification
  library: transformers
hub_signals:
  downloads_30d: 747876
  likes: 705
  last_modified: '2024-09-26'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/siglip-so400m-patch14-384
aliases:
- google/siglip-so400m-patch14-384
lineage:
  parents: []
  children: []
tags:
- zero-shot-image-classification
- siglip
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/google/siglip-so400m-patch14-384
  title: google/siglip-so400m-patch14-384 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/siglip-so400m-patch14-384/blob/main/config.json
  title: google/siglip-so400m-patch14-384 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2303.15343
  title: arXiv:2303.15343
  trust: 0.95
- url: https://arxiv.org/abs/2305.13035
  title: arXiv:2305.13035
  trust: 0.95
- url: https://arxiv.org/abs/2209.06794
  title: arXiv:2209.06794
  trust: 0.95
---


