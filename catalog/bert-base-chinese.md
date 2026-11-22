---
id: model/bert-base-chinese
type: model
slug: bert-base-chinese
schema_version: 1.1
title: bert-base-chinese
organization: organization/google-bert
license: license/apache-2-0
model_category: language
tasks:
- task/fill-mask
parameters_active_b: 0.1
total_parameters: 102882442
context_window_tokens: 512
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 512
  vocabulary_size: 21128
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: fill-mask
  library: transformers
hub_signals:
  downloads_30d: 313138
  likes: 1503
  last_modified: '2025-07-03'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google-bert/bert-base-chinese
aliases:
- google-bert/bert-base-chinese
lineage:
  parents: []
  children: []
tags:
- llm
- fill-mask
- bert
- dense
- mha
updated: '2026-11-22'
sources:
- url: https://huggingface.co/google-bert/bert-base-chinese
  title: google-bert/bert-base-chinese — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google-bert/bert-base-chinese/blob/main/config.json
  title: google-bert/bert-base-chinese — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1810.04805
  title: arXiv:1810.04805
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'transformers/models/bert/modeling_bert.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


