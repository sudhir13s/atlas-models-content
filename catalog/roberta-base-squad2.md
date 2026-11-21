---
id: model/roberta-base-squad2
type: model
slug: roberta-base-squad2
schema_version: 1.1
title: roberta-base-squad2
organization: organization/deepset
license_note: cc-by-4.0
model_category: language
tasks:
- task/question-answering
parameters_active_b: 0.12
total_parameters: 124057092
context_window_tokens: 514
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 514
  vocabulary_size: 50265
  activation_function: gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: question-answering
  library: transformers
hub_signals:
  downloads_30d: 351798
  likes: 979
  last_modified: '2024-09-24'
  observed_at: '2026-11-21'
  source: https://huggingface.co/deepset/roberta-base-squad2
aliases:
- deepset/roberta-base-squad2
lineage:
  parents: []
  children: []
tags:
- llm
- question-answering
- roberta
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/deepset/roberta-base-squad2
  title: deepset/roberta-base-squad2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepset/roberta-base-squad2/blob/main/config.json
  title: deepset/roberta-base-squad2 — config.json
  trust: 0.9
---


