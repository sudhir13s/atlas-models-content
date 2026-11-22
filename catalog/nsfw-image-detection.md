---
id: model/nsfw-image-detection
type: model
slug: nsfw-image-detection
schema_version: 1.1
title: nsfw_image_detection
organization: organization/falconsai
release_date: '2023-10-13'
release_year: 2023
release_month: 10
license: license/apache-2-0
model_category: vision
tasks:
- task/image-classification
parameters_active_b: 0.09
total_parameters: 85800194
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: pre
  stack: encoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: image-classification
  library: transformers
hub_signals:
  downloads_30d: 1565576
  likes: 1232
  last_modified: '2026-09-07'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Falconsai/nsfw_image_detection
aliases:
- Falconsai/nsfw_image_detection
lineage:
  parents: []
  children: []
tags:
- image-classification
- vit
- dense
- mha
updated: '2026-11-22'
sources:
- url: https://huggingface.co/Falconsai/nsfw_image_detection
  title: Falconsai/nsfw_image_detection — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Falconsai/nsfw_image_detection/blob/main/config.json
  title: Falconsai/nsfw_image_detection — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2010.11929
  title: arXiv:2010.11929
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vit/modeling_vit.py
  title: 'transformers/models/vit/modeling_vit.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


