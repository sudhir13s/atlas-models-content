---
id: model/openelm-1-1b-instruct
type: model
slug: openelm-1-1b-instruct
schema_version: 1.1
title: OpenELM-1_1B-Instruct
organization: organization/apple
release_date: '2024-04-12'
release_year: 2024
release_month: 4
license: license/apple-amlr
model_category: language
tasks:
- task/text-generation
parameters_active_b: 1.08
total_parameters: 1079891456
context_window_tokens: 2048
fingerprint:
  num_layers: 28
  head_size: 64
  hidden_size: 2048
  context_window_tokens: 2048
  vocabulary_size: 32000
  activation_function: swish
  normalization: RMSNorm
  positional_encoding: RoPE
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 916
  likes: 78
  last_modified: '2025-02-28'
  observed_at: '2026-11-19'
  source: https://huggingface.co/apple/OpenELM-1_1B-Instruct
aliases:
- apple/OpenELM-1_1B-Instruct
lineage:
  parents: []
  children: []
tags:
- llm
- text-generation
- openelm
- dense
updated: '2026-11-19'
sources:
- url: https://huggingface.co/apple/OpenELM-1_1B-Instruct
  title: apple/OpenELM-1_1B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/apple/OpenELM-1_1B-Instruct/blob/main/config.json
  title: apple/OpenELM-1_1B-Instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2404.14619
  title: arXiv:2404.14619
  trust: 0.95
---


