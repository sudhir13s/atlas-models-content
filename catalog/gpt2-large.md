---
id: model/gpt2-large
type: model
slug: gpt2-large
schema_version: 1.1
title: gpt2-large
organization: organization/openai
release_date: '2022-03-02'
release_year: 2022
release_month: 3
license: license/mit
architecture_id: architecture-era/gpt
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.81
total_parameters: 811778816
context_window_tokens: 1024
fingerprint:
  attention_type: MHA
  num_layers: 36
  attention_heads: 20
  kv_heads: 20
  head_size: 64
  hidden_size: 1280
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
  downloads_30d: 1096089
  likes: 375
  last_modified: '2024-02-19'
  observed_at: '2026-11-20'
  source: https://huggingface.co/openai-community/gpt2-large
aliases:
- openai-community/gpt2-large
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- gpt2
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/openai-community/gpt2-large
  title: openai-community/gpt2-large — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai-community/gpt2-large/blob/main/config.json
  title: openai-community/gpt2-large — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.09700
  title: arXiv:1910.09700
  trust: 0.95
---


