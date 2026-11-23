---
id: model/tapas-base-finetuned-sqa
type: model
slug: tapas-base-finetuned-sqa
schema_version: 3.0
title: tapas-base-finetuned-sqa
organization: organization/google-deepmind
license: license/apache-2-0
model_category: documents
model_kind: transformer-language
tasks:
- task/table-question-answering
context_window_tokens: 1024
summary: This is the base-size TAPAS model fine-tuned on SQA, the Sequential Question Answering dataset. It answers questions about a table in a conversational set-up, where each question can depend on the ones before it. TAPAS is a BERT-like transformer pretrained on tables and surrounding text from English Wikipedia. The Hugging Face team and contributors wrote the card, because the TAPAS team did not.
intended_use: The card says the model is for answering questions related to a table in a conversational set-up. It names no out-of-scope uses.
what_sets_it_apart: The card says TAPAS was pretrained with masked language modeling and then with an intermediate step meant to encourage numerical reasoning on tables. In that step the model learns to say whether a sentence is supported or refuted by a table, using millions of synthetic examples. The default version uses relative position embeddings, which reset the position index at every table cell. Fine-tuning adds a cell selection head and an inductive bias so the model only selects cells from the same column.
running_it: The card gives the input format as the question followed by the flattened table, with markers between them. Text is lowercased and tokenized with WordPiece and a 30,000-token vocabulary. Besides the default version, a no_reset branch gives the version with absolute position embeddings.
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
  output_head: cell-selection
  embedding_norm: true
  positional_encoding: Learned
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: table-question-answering
  library: transformers
hub_signals:
  downloads_30d: 1850
  likes: 14
  last_modified: '2021-11-29'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/tapas-base-finetuned-sqa
aliases:
- google/tapas-base-finetuned-sqa
lineage:
  parents: []
  children: []
quotes:
  summary: TAPAS is a BERT-like transformers model pretrained on a large corpus of English data from Wikipedia in a self-supervised fashion.
  intended_use: You can use this model for answering questions related to a table in a conversational set-up.
  what_sets_it_apart: 'Intermediate pre-training: to encourage numerical reasoning on tables, the authors additionally pre-trained the model by creating'
  running_it: '[CLS] Question [SEP] Flattened table [SEP]'
tags:
- tapas
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/tapas-base-finetuned-sqa
  title: google/tapas-base-finetuned-sqa — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/tapas-base-finetuned-sqa/blob/main/config.json
  title: google/tapas-base-finetuned-sqa — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2004.02349
  title: arXiv:2004.02349
  trust: 0.95
- url: https://arxiv.org/abs/2010.00571
  title: arXiv:2010.00571
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/tapas/modeling_tapas.py
  title: 'transformers/models/tapas/modeling_tapas.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


