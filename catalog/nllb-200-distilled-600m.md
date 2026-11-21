---
id: model/nllb-200-distilled-600m
type: model
slug: nllb-200-distilled-600m
schema_version: 1.1
title: nllb-200-distilled-600M
organization: organization/meta
release_date: '2022-07-08'
release_year: 2022
release_month: 7
license_note: cc-by-nc-4.0
model_category: language
tasks:
- task/translation
context_window_tokens: 1024
fingerprint:
  num_layers: 12
  hidden_size: 1024
  context_window_tokens: 1024
  vocabulary_size: 256206
  activation_function: relu
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: translation
  library: transformers
hub_signals:
  downloads_30d: 765198
  likes: 1017
  last_modified: '2024-02-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/nllb-200-distilled-600M
aliases:
- facebook/nllb-200-distilled-600M
lineage:
  parents: []
  children: []
tags:
- llm
- translation
- m2m-100
- dense
updated: '2026-11-21'
sources:
- url: https://huggingface.co/facebook/nllb-200-distilled-600M
  title: facebook/nllb-200-distilled-600M — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/nllb-200-distilled-600M/blob/main/config.json
  title: facebook/nllb-200-distilled-600M — config.json
  trust: 0.9
---


