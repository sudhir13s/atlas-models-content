---
id: model/tapas-base-finetuned-wtq
type: model
slug: tapas-base-finetuned-wtq
schema_version: 3.0
title: tapas-base-finetuned-wtq
organization: organization/google-deepmind
license: license/apache-2-0
model_category: documents
model_kind: transformer-language
tasks:
- task/table-question-answering
context_window_tokens: 1024
summary: 'TAPAS base fine-tuned on WikiTable Questions (WTQ) is a table question answering model: you give it a table and a question in English and it finds the answer in the table''s cells. TAPAS is a BERT-like model pretrained on English Wikipedia tables and their surrounding text, and the Hugging Face team wrote this card because the TAPAS authors did not publish one.'
intended_use: The card says the model can be used for answering questions related to a table, and points to the TAPAS documentation for code examples.
what_sets_it_apart: The card says the model was pretrained with masked language modeling and with an intermediate step that teaches numerical reasoning, by classifying whether a sentence is supported or refuted by a table. It was then fine-tuned in a chain on SQA, WikiSQL and finally WTQ, using a cell selection head and an aggregation head. This default version uses relative position embeddings, resetting the position index at every table cell.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  vocabulary_size: 30522
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder
  output_head: cell-selection-aggregation
  embedding_norm: true
  positional_encoding: Learned
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: table-question-answering
  library: transformers
hub_signals:
  downloads_30d: 11578
  likes: 248
  last_modified: '2022-07-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/tapas-base-finetuned-wtq
aliases:
- google/tapas-base-finetuned-wtq
lineage:
  parents: []
  children: []
quotes:
  summary: TAPAS is a BERT-like transformers model pretrained on a large corpus of English data from Wikipedia in a self-supervised fashion.
  intended_use: You can use this model for answering questions related to a table.
  what_sets_it_apart: It uses relative position embeddings (i.e. resetting the position index at every cell of the table).
tags:
- tapas
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/tapas-base-finetuned-wtq
  title: google/tapas-base-finetuned-wtq — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/tapas-base-finetuned-wtq/blob/main/config.json
  title: google/tapas-base-finetuned-wtq — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2004.02349
  title: arXiv:2004.02349
  trust: 0.95
- url: https://arxiv.org/abs/2010.00571
  title: arXiv:2010.00571
  trust: 0.95
- url: https://arxiv.org/abs/1508.00305
  title: arXiv:1508.00305
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/tapas/modeling_tapas.py
  title: 'transformers/models/tapas/modeling_tapas.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


