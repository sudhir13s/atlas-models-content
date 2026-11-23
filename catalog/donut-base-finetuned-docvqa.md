---
id: model/donut-base-finetuned-docvqa
type: model
slug: donut-base-finetuned-docvqa
schema_version: 1.1
title: donut-base-finetuned-docvqa
organization: organization/naver-clova-ix
release_date: '2022-07-19'
release_year: 2022
release_month: 7
license: license/mit
model_category: documents
tasks:
- task/document-question-answering
context_window_tokens: 128
summary: 'Donut base fine-tuned on DocVQA is a document question answering model: given an image of a document and a question about it, it writes the answer as text. The card says this is Donut fine-tuned on DocVQA, a document visual question answering dataset, and that Donut was introduced in the paper OCR-free Document Understanding Transformer. The Hugging Face team wrote the card, not the team that released Donut.'
intended_use: The card says only that the model is fine-tuned on DocVQA and refers readers to the Transformers documentation for code examples. The example questions in its metadata, "What is the invoice number?" and "What is the purchase amount?", show the kind of question it is meant to answer from scans of invoices and contracts.
what_sets_it_apart: The card describes two parts. A vision encoder, a Swin Transformer, turns the image into a set of embeddings, and a text decoder, BART, then writes text one token at a time based on that encoding. The paper title says the approach needs no OCR (optical character recognition), so the page image is read directly rather than first passed through a separate text-extraction step.
fingerprint:
  num_layers: 12
  hidden_size: 1024
  context_window_tokens: 128
  vocabulary_size: 57532
  activation_function: gelu
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: document-question-answering
  library: transformers
hub_signals:
  downloads_30d: 7392
  likes: 285
  last_modified: '2024-03-09'
  observed_at: '2026-11-21'
  source: https://huggingface.co/naver-clova-ix/donut-base-finetuned-docvqa
aliases:
- naver-clova-ix/donut-base-finetuned-docvqa
lineage:
  parents: []
  children: []
quotes:
  summary: Donut model fine-tuned on DocVQA.
  intended_use: This model is fine-tuned on DocVQA, a document visual question answering dataset.
  what_sets_it_apart: Donut consists of a vision encoder (Swin Transformer) and a text decoder (BART).
tags:
- document-question-answering
- vision-encoder-decoder
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/naver-clova-ix/donut-base-finetuned-docvqa
  title: naver-clova-ix/donut-base-finetuned-docvqa — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/naver-clova-ix/donut-base-finetuned-docvqa/blob/main/config.json
  title: naver-clova-ix/donut-base-finetuned-docvqa — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2111.15664
  title: arXiv:2111.15664
  trust: 0.95
---

## Summary

Donut base fine-tuned on DocVQA is a document question answering model: given an image of a document and a question about it, it writes the answer as text. The card says this is Donut fine-tuned on DocVQA, a document visual question answering dataset, and that Donut was introduced in the paper OCR-free Document Understanding Transformer. The Hugging Face team wrote the card, not the team that released Donut.

## Intended Use

The card says only that the model is fine-tuned on DocVQA and refers readers to the Transformers documentation for code examples. The example questions in its metadata, "What is the invoice number?" and "What is the purchase amount?", show the kind of question it is meant to answer from scans of invoices and contracts.

## What Sets It Apart

The card describes two parts. A vision encoder, a Swin Transformer, turns the image into a set of embeddings, and a text decoder, BART, then writes text one token at a time based on that encoding. The paper title says the approach needs no OCR (optical character recognition), so the page image is read directly rather than first passed through a separate text-extraction step.
