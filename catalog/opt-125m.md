---
id: model/opt-125m
type: model
slug: opt-125m
schema_version: 1.1
title: opt-125m
organization: organization/meta
release_date: '2022-05-11'
release_year: 2022
release_month: 5
license_note: other
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
context_window_tokens: 2048
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 2048
  vocabulary_size: 50272
  activation_function: relu
  positional_encoding: Learned
filters:
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 4497895
  likes: 334
  last_modified: '2023-09-15'
  observed_at: '2026-11-20'
  source: https://huggingface.co/facebook/opt-125m
aliases:
- facebook/opt-125m
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- opt
- dense
- mha
updated: '2026-11-20'
sources:
- url: https://huggingface.co/facebook/opt-125m
  title: facebook/opt-125m — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/opt-125m/blob/main/config.json
  title: facebook/opt-125m — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2205.01068
  title: arXiv:2205.01068
  trust: 0.95
- url: https://arxiv.org/abs/2005.14165
  title: arXiv:2005.14165
  trust: 0.95
---


