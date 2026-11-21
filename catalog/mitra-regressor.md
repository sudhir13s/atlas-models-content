---
id: model/mitra-regressor
type: model
slug: mitra-regressor
schema_version: 1.1
title: mitra-regressor
organization: organization/autogluon
release_date: '2025-06-22'
release_year: 2025
release_month: 6
license: license/apache-2-0
model_category: structured-data
tasks:
- task/tabular-regression
parameters_active_b: 0.08
total_parameters: 75661313
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 4
  kv_heads: 4
  head_size: 128
  hidden_size: 512
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: tabular-regression
hub_signals:
  downloads_30d: 186891
  likes: 33
  last_modified: '2025-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/autogluon/mitra-regressor
aliases:
- autogluon/mitra-regressor
lineage:
  parents: []
  children: []
tags:
- tabular-regression
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/autogluon/mitra-regressor
  title: autogluon/mitra-regressor — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/autogluon/mitra-regressor/blob/main/config.json
  title: autogluon/mitra-regressor — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2510.21204
  title: arXiv:2510.21204
  trust: 0.95
---


