---
id: model/distilbert-base-cased-distilled-squad
type: model
slug: distilbert-base-cased-distilled-squad
schema_version: 1.1
title: distilbert-base-cased-distilled-squad
organization: organization/distilbert
license: license/apache-2-0
model_category: language
tasks:
- task/question-answering
parameters_active_b: 0.07
total_parameters: 65192450
context_window_tokens: 512
fingerprint:
  attention_type: MHA
  num_layers: 6
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  context_window_tokens: 512
  vocabulary_size: 28996
  norm_placement: post
  stack: encoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: question-answering
  library: transformers
hub_signals:
  downloads_30d: 154153
  likes: 279
  last_modified: '2024-05-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/distilbert/distilbert-base-cased-distilled-squad
aliases:
- distilbert/distilbert-base-cased-distilled-squad
lineage:
  parents: []
  children: []
tags:
- llm
- question-answering
- distilbert
- dense
- mha
updated: '2026-11-22'
sources:
- url: https://huggingface.co/distilbert/distilbert-base-cased-distilled-squad
  title: distilbert/distilbert-base-cased-distilled-squad — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/distilbert/distilbert-base-cased-distilled-squad/blob/main/config.json
  title: distilbert/distilbert-base-cased-distilled-squad — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.01108
  title: arXiv:1910.01108
  trust: 0.95
- url: https://arxiv.org/abs/1910.09700
  title: arXiv:1910.09700
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/distilbert/modeling_distilbert.py
  title: 'transformers/models/distilbert/modeling_distilbert.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


