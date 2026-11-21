---
id: model/table-transformer-detection
type: model
slug: table-transformer-detection
schema_version: 1.1
title: table-transformer-detection
organization: organization/microsoft
release_date: '2022-10-14'
release_year: 2022
release_month: 10
license: license/mit
model_category: vision
tasks:
- task/object-detection
parameters_active_b: 0.03
total_parameters: 28818631
context_window_tokens: 1024
fingerprint:
  num_layers: 6
  hidden_size: 256
  context_window_tokens: 1024
  activation_function: relu
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: object-detection
  library: transformers
hub_signals:
  downloads_30d: 295500
  likes: 445
  last_modified: '2023-09-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/microsoft/table-transformer-detection
aliases:
- microsoft/table-transformer-detection
lineage:
  parents: []
  children: []
tags:
- object-detection
- table-transformer
- dense
updated: '2026-11-21'
sources:
- url: https://huggingface.co/microsoft/table-transformer-detection
  title: microsoft/table-transformer-detection — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/table-transformer-detection/blob/main/config.json
  title: microsoft/table-transformer-detection — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2110.00061
  title: arXiv:2110.00061
  trust: 0.95
---


