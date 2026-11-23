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
summary: 'TAPEX base fine-tuned on WikiSQL is a table question answering model: given a table and a question in English, it writes the answer. It is the tapex-base model, fine-tuned on the WikiSQL dataset. TAPEX, short for Table Pre-training via Execution, is a pre-training approach that teaches a model to reason over tables, and its original code is in the microsoft/Table-Pretraining repository.'
intended_use: The card says the model can be used for table question answering on relatively simple questions, and gives examples such as asking what position a player plays or how many schools a numbered player attended. It says nothing further on out-of-scope uses.
what_sets_it_apart: The card says TAPEX learns table reasoning by training a neural SQL executor on a synthetic corpus built from automatically generated executable SQL queries. It is based on the BART architecture, a sequence-to-sequence model with a bidirectional encoder and an autoregressive decoder.
running_it: The card's example notes that TAPEX accepts uncased input, because it was pre-trained on an uncased corpus.
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
quotes:
  summary: This model is the `tapex-base` model fine-tuned on the
  intended_use: You can use the model for table question answering on relatively simple questions.
  what_sets_it_apart: TAPEX realizes table pre-training by learning a neural SQL executor over a synthetic corpus
  running_it: tapex accepts uncased input since it is pre-trained on the uncased corpus
tags:
- table-question-answering
- bart
- dense
updated: '2026-11-23'
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

## Summary

TAPEX base fine-tuned on WikiSQL is a table question answering model: given a table and a question in English, it writes the answer. It is the tapex-base model, fine-tuned on the WikiSQL dataset. TAPEX, short for Table Pre-training via Execution, is a pre-training approach that teaches a model to reason over tables, and its original code is in the microsoft/Table-Pretraining repository.

## Intended Use

The card says the model can be used for table question answering on relatively simple questions, and gives examples such as asking what position a player plays or how many schools a numbered player attended. It says nothing further on out-of-scope uses.

## What Sets It Apart

The card says TAPEX learns table reasoning by training a neural SQL executor on a synthetic corpus built from automatically generated executable SQL queries. It is based on the BART architecture, a sequence-to-sequence model with a bidirectional encoder and an autoregressive decoder.

## Running It

The card's example notes that TAPEX accepts uncased input, because it was pre-trained on an uncased corpus.
