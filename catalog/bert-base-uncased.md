---
id: model/bert-base-uncased
type: model
slug: bert-base-uncased
schema_version: 1.1
title: bert-base-uncased
organization: organization/google-bert
license: license/apache-2-0
model_category: language
tasks:
- task/fill-mask
parameters_active_b: 0.11
total_parameters: 110106428
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
  vocabulary_size: 30522
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder
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
  downloads_30d: 33456723
  likes: 3084
  last_modified: '2024-02-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google-bert/bert-base-uncased
aliases:
- google-bert/bert-base-uncased
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
- url: https://huggingface.co/google-bert/bert-base-uncased
  title: google-bert/bert-base-uncased — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: google-bert/bert-base-uncased — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1810.04805
  title: arXiv:1810.04805
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'transformers/models/bert/modeling_bert.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


