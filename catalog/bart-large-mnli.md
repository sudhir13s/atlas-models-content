---
id: model/bart-large-mnli
type: model
slug: bart-large-mnli
schema_version: 1.1
title: bart-large-mnli
organization: organization/meta
license: license/mit
model_category: language
tasks:
- task/zero-shot-classification
parameters_active_b: 0.41
total_parameters: 407344133
context_window_tokens: 1024
fingerprint:
  num_layers: 12
  hidden_size: 1024
  context_window_tokens: 1024
  vocabulary_size: 50265
  activation_function: gelu
  norm_placement: post
  stack: encoder-decoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: zero-shot-classification
  library: transformers
hub_signals:
  downloads_30d: 3653291
  likes: 1641
  last_modified: '2023-09-05'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/bart-large-mnli
aliases:
- facebook/bart-large-mnli
lineage:
  parents: []
  children: []
tags:
- llm
- zero-shot-classification
- bart
- dense
updated: '2026-11-22'
sources:
- url: https://huggingface.co/facebook/bart-large-mnli
  title: facebook/bart-large-mnli — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/bart-large-mnli/blob/main/config.json
  title: facebook/bart-large-mnli — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.13461
  title: arXiv:1910.13461
  trust: 0.95
- url: https://arxiv.org/abs/1909.00161
  title: arXiv:1909.00161
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bart/modeling_bart.py
  title: 'transformers/models/bart/modeling_bart.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


