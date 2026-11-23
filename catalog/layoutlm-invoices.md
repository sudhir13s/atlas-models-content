---
id: model/layoutlm-invoices
type: model
slug: layoutlm-invoices
schema_version: 1.1
title: layoutlm-invoices
organization: organization/impira
release_date: '2022-09-06'
release_year: 2022
release_month: 9
license_note: cc-by-nc-sa-4.0
model_category: documents
tasks:
- task/document-question-answering
parameters_active_b: 0.13
total_parameters: 127794950
context_window_tokens: 514
summary: LayoutLM for Invoices is a document question-answering model from Impira. You give it an image of an invoice or similar document and a question such as the invoice number, and it finds the answer in the text on the page. The card says it is a fine-tuned version of the multi-modal LayoutLM model, which reads both the words and where they sit on the page, trained on a proprietary invoice dataset plus SQuAD2.0 and DocVQA.
intended_use: The card says it is for question answering on invoices and other documents, and that the best way to use it is through Impira's DocQuery tool.
what_sets_it_apart: The card says that, unlike other question-answering models that can only extract consecutive tokens, this one can predict longer-range, non-consecutive sequences through an additional classifier head. Its example is an address that spans two lines, which ordinary models break apart.
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
  downloads_30d: 2896
  likes: 229
  last_modified: '2023-03-25'
  observed_at: '2026-11-21'
  source: https://huggingface.co/impira/layoutlm-invoices
aliases:
- impira/layoutlm-invoices
lineage:
  parents: []
  children: []
quotes:
  summary: This is a fine-tuned version of the multi-modal [LayoutLM](https://aka.ms/layoutlm) model for the task of question answering on invoices and other documents.
  intended_use: The best way to use this model is via [DocQuery](https://github.com/impira/docquery).
  what_sets_it_apart: this model can predict longer-range, non-consecutive sequences with an additional classifier head.
tags:
- document-question-answering
- layoutlm
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/impira/layoutlm-invoices
  title: impira/layoutlm-invoices — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/impira/layoutlm-invoices/blob/main/config.json
  title: impira/layoutlm-invoices — config.json
  trust: 0.9
---

## Summary

LayoutLM for Invoices is a document question-answering model from Impira. You give it an image of an invoice or similar document and a question such as the invoice number, and it finds the answer in the text on the page. The card says it is a fine-tuned version of the multi-modal LayoutLM model, which reads both the words and where they sit on the page, trained on a proprietary invoice dataset plus SQuAD2.0 and DocVQA.

## Intended Use

The card says it is for question answering on invoices and other documents, and that the best way to use it is through Impira's DocQuery tool.

## What Sets It Apart

The card says that, unlike other question-answering models that can only extract consecutive tokens, this one can predict longer-range, non-consecutive sequences through an additional classifier head. Its example is an address that spans two lines, which ordinary models break apart.
