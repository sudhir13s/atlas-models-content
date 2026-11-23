---
id: model/layoutlm-document-qa
type: model
slug: layoutlm-document-qa
schema_version: 3.0
title: layoutlm-document-qa
organization: organization/impira
release_date: '2022-08-07'
license: license/mit
model_category: documents
model_kind: transformer-language
tasks:
- task/document-question-answering
parameters_active_b: 0.13
total_parameters: 127793412
context_window_tokens: 514
summary: 'LayoutLM for Visual Question Answering is a document question answering model: given an image or PDF of a document, such as an invoice, and a question, it returns the answer found in the document. The card says it was created by the team at Impira as a fine-tuned version of the multi-modal LayoutLM model, trained further on the SQuAD2.0 and DocVQA datasets.'
intended_use: The card presents it for question answering on documents. Its example questions, for invoices and statements, are "What is the invoice number?", "What is the purchase amount?" and "What are the 2020 net sales?".
what_sets_it_apart: The card describes a multi-modal model, meaning it reads more than plain text, fine-tuned on both SQuAD2.0 and DocVQA. SQuAD2.0 is a text question answering dataset and DocVQA is a document question answering dataset.
running_it: The card says the example code needs PIL, pytesseract, PyTorch and transformers installed. It adds that the model and pipeline recently landed in transformers, so a recent version, or one installed from the source repository, is required.
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
quotes:
  summary: This is a fine-tuned version of the multi-modal
  intended_use: model for the task of question answering on documents
  what_sets_it_apart: It has been fine-tuned using both the
  running_it: you'll need to use a recent version of transformers
tags:
- layoutlm
updated: '2026-11-23'
sources:
- url: https://huggingface.co/impira/layoutlm-document-qa
  title: impira/layoutlm-document-qa — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/impira/layoutlm-document-qa/blob/main/config.json
  title: impira/layoutlm-document-qa — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/layoutlm/modeling_layoutlm.py
  title: 'transformers/models/layoutlm/modeling_layoutlm.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


