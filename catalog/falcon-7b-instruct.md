---
id: model/falcon-7b-instruct
type: model
slug: falcon-7b-instruct
schema_version: 1.1
title: falcon-7b-instruct
organization: organization/technology-innovation-institute
release_date: '2023-04-25'
release_year: 2023
release_month: 4
license: license/apache-2-0
model_category: language
parameters_active_b: 7.22
total_parameters: 7217189760
fingerprint:
  attention_type: MQA
  num_layers: 32
  attention_heads: 71
  kv_heads: 1
  head_size: 64
  hidden_size: 4544
  vocabulary_size: 65024
  normalization: LayerNorm
  positional_encoding: RoPE
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: MQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 45635
  likes: 1042
  last_modified: '2024-10-12'
  observed_at: '2026-11-19'
  source: https://huggingface.co/tiiuae/falcon-7b-instruct
aliases:
- tiiuae/falcon-7b-instruct
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- falcon
- dense
- mqa
updated: '2026-11-19'
sources:
- url: https://huggingface.co/tiiuae/falcon-7b-instruct
  title: tiiuae/falcon-7b-instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/tiiuae/falcon-7b-instruct/blob/main/config.json
  title: tiiuae/falcon-7b-instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2205.14135
  title: arXiv:2205.14135
  trust: 0.95
- url: https://arxiv.org/abs/1911.02150
  title: arXiv:1911.02150
  trust: 0.95
- url: https://arxiv.org/abs/2005.14165
  title: arXiv:2005.14165
  trust: 0.95
- url: https://arxiv.org/abs/2104.09864
  title: arXiv:2104.09864
  trust: 0.95
- url: https://arxiv.org/abs/2306.01116
  title: arXiv:2306.01116
  trust: 0.95
---


