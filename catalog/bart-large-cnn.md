---
id: model/bart-large-cnn
type: model
slug: bart-large-cnn
schema_version: 1.1
title: bart-large-cnn
organization: organization/meta
license: license/mit
model_category: language
tasks:
- task/summarization
parameters_active_b: 0.41
total_parameters: 406290432
context_window_tokens: 1024
fingerprint:
  num_layers: 12
  hidden_size: 1024
  context_window_tokens: 1024
  vocabulary_size: 50264
  activation_function: gelu
  norm_placement: post
  stack: encoder-decoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: summarization
  library: transformers
hub_signals:
  downloads_30d: 1052842
  likes: 1654
  last_modified: '2024-02-13'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/bart-large-cnn
aliases:
- facebook/bart-large-cnn
lineage:
  parents: []
  children: []
tags:
- llm
- summarization
- bart
- dense
updated: '2026-11-22'
sources:
- url: https://huggingface.co/facebook/bart-large-cnn
  title: facebook/bart-large-cnn — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/bart-large-cnn/blob/main/config.json
  title: facebook/bart-large-cnn — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.13461
  title: arXiv:1910.13461
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bart/modeling_bart.py
  title: 'transformers/models/bart/modeling_bart.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


