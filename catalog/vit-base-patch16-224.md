---
id: model/vit-base-patch16-224
type: model
slug: vit-base-patch16-224
schema_version: 1.1
title: vit-base-patch16-224
organization: organization/google-deepmind
license: license/apache-2-0
model_category: vision
tasks:
- task/image-classification
parameters_active_b: 0.09
total_parameters: 86567656
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
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: image-classification
  library: transformers
hub_signals:
  downloads_30d: 15301431
  likes: 1058
  last_modified: '2023-09-05'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/vit-base-patch16-224
aliases:
- google/vit-base-patch16-224
lineage:
  parents: []
  children: []
tags:
- image-classification
- vit
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/google/vit-base-patch16-224
  title: google/vit-base-patch16-224 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/vit-base-patch16-224/blob/main/config.json
  title: google/vit-base-patch16-224 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2010.11929
  title: arXiv:2010.11929
  trust: 0.95
- url: https://arxiv.org/abs/2006.03677
  title: arXiv:2006.03677
  trust: 0.95
---


