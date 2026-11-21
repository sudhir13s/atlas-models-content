---
id: model/tapex-base-finetuned-wikisql
type: model
slug: tapex-base-finetuned-wikisql
schema_version: 1.1
title: tapex-base-finetuned-wikisql
organization: organization/microsoft
license: license/mit
model_category: documents
tasks:
- task/table-question-answering
context_window_tokens: 1024
fingerprint:
  num_layers: 6
  hidden_size: 768
  context_window_tokens: 1024
  vocabulary_size: 50265
  activation_function: gelu
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: table-question-answering
  library: transformers
hub_signals:
  downloads_30d: 1182
  likes: 40
  last_modified: '2023-01-24'
  observed_at: '2026-11-21'
  source: https://huggingface.co/microsoft/tapex-base-finetuned-wikisql
aliases:
- microsoft/tapex-base-finetuned-wikisql
lineage:
  parents: []
  children: []
tags:
- table-question-answering
- bart
- dense
updated: '2026-11-21'
sources:
- url: https://huggingface.co/microsoft/tapex-base-finetuned-wikisql
  title: microsoft/tapex-base-finetuned-wikisql — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/tapex-base-finetuned-wikisql/blob/main/config.json
  title: microsoft/tapex-base-finetuned-wikisql — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2107.07653
  title: arXiv:2107.07653
  trust: 0.95
---


