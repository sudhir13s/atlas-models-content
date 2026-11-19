---
id: model/gpt2
type: model
slug: gpt2
schema_version: 1.1
title: gpt2
organization: organization/openai
release_date: '2022-03-02'
release_year: 2022
release_month: 3
license: license/mit
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.14
total_parameters: 137022720
context_window_tokens: 1024
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
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
  downloads_30d: 34336215
  likes: 3952
  last_modified: '2024-02-19'
  observed_at: '2026-11-19'
  source: https://huggingface.co/openai-community/gpt2
aliases:
- openai-community/gpt2
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
- url: https://huggingface.co/openai-community/gpt2
  title: openai-community/gpt2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai-community/gpt2/blob/main/config.json
  title: openai-community/gpt2 — config.json
  trust: 0.9
---


