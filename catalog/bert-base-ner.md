---
id: model/bert-base-ner
type: model
slug: bert-base-ner
schema_version: 1.1
title: bert-base-NER
organization: organization/dslim
license: license/mit
model_category: language
tasks:
- task/token-classification
parameters_active_b: 0.11
total_parameters: 108317193
context_window_tokens: 512
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 512
  vocabulary_size: 28996
  activation_function: gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: token-classification
  library: transformers
hub_signals:
  downloads_30d: 921050
  likes: 752
  last_modified: '2024-10-08'
  observed_at: '2026-11-21'
  source: https://huggingface.co/dslim/bert-base-NER
aliases:
- dslim/bert-base-NER
lineage:
  parents: []
  children: []
tags:
- llm
- token-classification
- bert
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/dslim/bert-base-NER
  title: dslim/bert-base-NER — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/dslim/bert-base-NER/blob/main/config.json
  title: dslim/bert-base-NER — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1810.04805
  title: arXiv:1810.04805
  trust: 0.95
---


