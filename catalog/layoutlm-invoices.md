---
id: model/layoutlm-invoices
type: model
slug: layoutlm-invoices
schema_version: 3.0
title: layoutlm-invoices
organization: organization/impira
release_date: '2022-09-06'
license_note: cc-by-nc-sa-4.0
model_category: documents
model_kind: transformer-language
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
  vocabulary_size: 50265
  activation_function: gelu
  normalization: LayerNorm
  positional_encoding: Learned
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/layoutlm/modeling_layoutlm.py
  summary: The model reads the words of a page together with where each word sits on the page. Every word's vector is the sum of its token, its place in the text and its box on the page; a transformer encoder then marks where the answer starts and ends.
  stages:
  - id: words
    role: input
    title: Words + their boxes
    details:
    - question, then the page's words
  - id: tokens
    role: embedding
    title: Token + position embedding
    details:
    - vocabulary 50,265 · width 768
  - id: boxes
    role: positionalEncoding
    title: Box position embeddings
    details:
    - left, top, right, bottom, height
    - and width of each word's box
    note: Six learned embeddings place each word on the page. They are added to the word's token and position embeddings.
  - id: norm
    role: normalisation
    title: Embedding LayerNorm
  - id: block
    role: attention
    title: Attention + feed-forward
    details:
    - 12 heads · norm after each add
    frame: Encoder block
    repeat: 12
  - id: head
    role: outputHead
    title: Answer span head
    details:
    - start and end score per token
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
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
- layoutlm
updated: '2026-11-23'
sources:
- url: https://huggingface.co/impira/layoutlm-invoices
  title: impira/layoutlm-invoices — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/impira/layoutlm-invoices/blob/main/config.json
  title: impira/layoutlm-invoices — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/layoutlm/modeling_layoutlm.py
  title: 'transformers/models/layoutlm/modeling_layoutlm.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


