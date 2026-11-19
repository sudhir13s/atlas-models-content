---
id: model/gpt2-medium
type: model
slug: gpt2-medium
schema_version: 1.1
title: gpt2-medium
organization: organization/openai
release_date: '2022-03-02'
release_year: 2022
release_month: 3
license: license/mit
model_category: language
parameters_active_b: 0.38
total_parameters: 379988992
context_window_tokens: 1024
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  context_window_tokens: 1024
  vocabulary_size: 50257
  activation_function: gelu_new
  normalization: LayerNorm
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 153642
  likes: 211
  last_modified: '2024-02-19'
  observed_at: '2026-11-19'
  source: https://huggingface.co/openai-community/gpt2-medium
aliases:
- openai-community/gpt2-medium
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- gpt2
- dense
- mha
updated: '2026-11-19'
sources:
- url: https://huggingface.co/openai-community/gpt2-medium
  title: openai-community/gpt2-medium — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai-community/gpt2-medium/blob/main/config.json
  title: openai-community/gpt2-medium — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.09700
  title: arXiv:1910.09700
  trust: 0.95
---


