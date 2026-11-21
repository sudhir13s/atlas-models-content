---
id: model/dpt-large
type: model
slug: dpt-large
schema_version: 1.1
title: dpt-large
organization: organization/intel
license: license/apache-2-0
model_category: vision
tasks:
- task/depth-estimation
parameters_active_b: 0.34
total_parameters: 341850305
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  intermediate_size: 4096
  activation_function: gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: depth-estimation
  library: transformers
hub_signals:
  downloads_30d: 27715
  likes: 208
  last_modified: '2024-02-24'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Intel/dpt-large
aliases:
- Intel/dpt-large
lineage:
  parents: []
  children: []
tags:
- depth-estimation
- dpt
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/Intel/dpt-large
  title: Intel/dpt-large — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Intel/dpt-large/blob/main/config.json
  title: Intel/dpt-large — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2103.13413
  title: arXiv:2103.13413
  trust: 0.95
---


