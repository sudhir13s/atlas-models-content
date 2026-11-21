---
id: model/layoutlm-document-qa
type: model
slug: layoutlm-document-qa
schema_version: 1.1
title: layoutlm-document-qa
organization: organization/impira
release_date: '2022-08-07'
release_year: 2022
release_month: 8
license: license/mit
model_category: documents
tasks:
- task/document-question-answering
parameters_active_b: 0.13
total_parameters: 127793412
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
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: document-question-answering
  library: transformers
hub_signals:
  downloads_30d: 362627
  likes: 1224
  last_modified: '2023-03-18'
  observed_at: '2026-11-21'
  source: https://huggingface.co/impira/layoutlm-document-qa
aliases:
- impira/layoutlm-document-qa
lineage:
  parents: []
  children: []
tags:
- document-question-answering
- layoutlm
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/impira/layoutlm-document-qa
  title: impira/layoutlm-document-qa — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/impira/layoutlm-document-qa/blob/main/config.json
  title: impira/layoutlm-document-qa — config.json
  trust: 0.9
---


