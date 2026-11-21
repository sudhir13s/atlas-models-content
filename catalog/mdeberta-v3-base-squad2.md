---
id: model/mdeberta-v3-base-squad2
type: model
slug: mdeberta-v3-base-squad2
schema_version: 1.1
title: mdeberta-v3-base-squad2
organization: organization/timpal0l
release_date: '2022-11-30'
release_year: 2022
release_month: 11
license: license/mit
model_category: language
tasks:
- task/question-answering
parameters_active_b: 0.28
total_parameters: 278220802
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
  vocabulary_size: 251000
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
  downloads_30d: 6423
  likes: 260
  last_modified: '2024-11-26'
  observed_at: '2026-11-21'
  source: https://huggingface.co/timpal0l/mdeberta-v3-base-squad2
aliases:
- timpal0l/mdeberta-v3-base-squad2
lineage:
  parents: []
  children: []
tags:
- llm
- question-answering
- deberta-v2
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/timpal0l/mdeberta-v3-base-squad2
  title: timpal0l/mdeberta-v3-base-squad2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/timpal0l/mdeberta-v3-base-squad2/blob/main/config.json
  title: timpal0l/mdeberta-v3-base-squad2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2006.03654
  title: arXiv:2006.03654
  trust: 0.95
- url: https://arxiv.org/abs/2111.09543
  title: arXiv:2111.09543
  trust: 0.95
---


