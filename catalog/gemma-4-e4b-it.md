---
id: model/gemma-4-e4b-it
type: model
slug: gemma-4-e4b-it
schema_version: 1.1
title: gemma-4-E4B-it
organization: organization/google-deepmind
release_date: '2026-03-02'
release_year: 2026
release_month: 3
license: license/apache-2-0
model_category: multimodal
tasks:
- task/any-to-any-models
parameters_active_b: 8.0
total_parameters: 7996156490
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 42
  attention_heads: 8
  kv_heads: 2
  head_size: 256
  hidden_size: 2560
  intermediate_size: 10240
  context_window_tokens: 131072
  vocabulary_size: 262144
  normalization: RMSNorm
  positional_encoding: RoPE
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: any-to-any
  library: transformers
hub_signals:
  downloads_30d: 4326816
  likes: 1890
  last_modified: '2026-11-03'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/gemma-4-E4B-it
aliases:
- google/gemma-4-E4B-it
lineage:
  parents: []
  children: []
tags:
- any-to-any
- gemma4
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/google/gemma-4-E4B-it
  title: google/gemma-4-E4B-it — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/gemma-4-E4B-it/blob/main/config.json
  title: google/gemma-4-E4B-it — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2607.02770
  title: arXiv:2607.02770
  trust: 0.95
---


