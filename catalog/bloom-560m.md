---
id: model/bloom-560m
type: model
slug: bloom-560m
schema_version: 1.1
title: bloom-560m
organization: organization/bigscience
release_date: '2022-05-19'
release_year: 2022
release_month: 5
license: license/bigscience-bloom-rail-1-0
model_category: language
tasks:
- task/text-generation
parameters_active_b: 0.56
total_parameters: 559214592
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  vocabulary_size: 250880
  normalization: LayerNorm
  positional_encoding: ALiBi
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 387269
  likes: 376
  last_modified: '2023-09-26'
  observed_at: '2026-11-19'
  source: https://huggingface.co/bigscience/bloom-560m
aliases:
- bigscience/bloom-560m
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- bloom
- dense
- mha
updated: '2026-11-19'
sources:
- url: https://huggingface.co/bigscience/bloom-560m
  title: bigscience/bloom-560m — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/bigscience/bloom-560m/blob/main/config.json
  title: bigscience/bloom-560m — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1909.08053
  title: arXiv:1909.08053
  trust: 0.95
- url: https://arxiv.org/abs/2110.02861
  title: arXiv:2110.02861
  trust: 0.95
- url: https://arxiv.org/abs/2108.12409
  title: arXiv:2108.12409
  trust: 0.95
---


