---
id: model/detr-resnet-50
type: model
slug: detr-resnet-50
schema_version: 1.1
title: detr-resnet-50
organization: organization/meta
license: license/apache-2-0
model_category: vision
tasks:
- task/object-detection
parameters_active_b: 0.04
total_parameters: 41631008
context_window_tokens: 1024
fingerprint:
  num_layers: 6
  hidden_size: 256
  context_window_tokens: 1024
  activation_function: relu
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: object-detection
  library: transformers
hub_signals:
  downloads_30d: 113423
  likes: 991
  last_modified: '2024-04-10'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/detr-resnet-50
aliases:
- facebook/detr-resnet-50
lineage:
  parents: []
  children: []
tags:
- object-detection
- detr
- dense
updated: '2026-11-21'
sources:
- url: https://huggingface.co/facebook/detr-resnet-50
  title: facebook/detr-resnet-50 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/detr-resnet-50/blob/main/config.json
  title: facebook/detr-resnet-50 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2005.12872
  title: arXiv:2005.12872
  trust: 0.95
---


