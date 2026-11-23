---
id: model/unlimited-ocr
type: model
slug: unlimited-ocr
schema_version: 1.1
title: Unlimited-OCR
organization: organization/baidu
release_date: '2026-06-19'
release_year: 2026
release_month: 6
license: license/mit
model_category: documents
tasks:
- task/ocr
- task/vision-language-models
total_parameters: 3336106240
context_window_tokens: 32768
summary: Unlimited OCR is a document-reading model from Baidu. OCR, optical character recognition, means turning an image of a page into text. The accompanying paper takes DeepSeek OCR as its baseline and says the model can transcribe dozens of pages of documents in a single forward pass under a standard maximum length of 32K.
intended_use: The card shows parsing single images in two configurations, called gundam and base, and parsing multi-page documents and PDFs, where each PDF page is first converted to an image and only the base configuration is used. The paper's authors add that the same attention mechanism can also apply to speech recognition and translation.
what_sets_it_apart: The paper points out that a language-model decoder keeps a growing store of past attention data, the KV cache, which raises memory use and slows generation as the output gets longer. It replaces every attention layer in the decoder with Reference Sliding Window Attention, which the authors say reduces attention computation and keeps the KV cache constant for the whole decoding process. It keeps the high-compression encoder of DeepSeek OCR.
fingerprint:
  attention_type: MLA
  num_layers: 12
  attention_heads: 10
  kv_heads: 10
  head_size: 128
  hidden_size: 1280
  intermediate_size: 6848
  context_window_tokens: 32768
  vocabulary_size: 129280
moe_config:
  expert_count: 64
  active_experts: 6
  shared_expert: true
filters:
  size_category: small
  is_quantized: false
  is_moe: true
  attention_type: MLA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 5600129
  likes: 5023
  last_modified: '2026-10-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/baidu/Unlimited-OCR
aliases:
- baidu/Unlimited-OCR
lineage:
  parents: []
  children: []
quotes:
  summary: Unlimited OCR can transcribe dozens of pages of documents in a single forward pass under a standard maximum length of 32K.
  intended_use: Multi page / PDF only uses base (image_size=1024)
  what_sets_it_apart: we replace all attention layers in the decoder with our proposed Reference Sliding Window Attention (R-SWA)
tags:
- image-text-to-text
- unlimited-ocr
- moe
- mla
updated: '2026-11-23'
sources:
- url: https://huggingface.co/baidu/Unlimited-OCR
  title: baidu/Unlimited-OCR — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/baidu/Unlimited-OCR/blob/main/config.json
  title: baidu/Unlimited-OCR — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2606.23050
  title: arXiv:2606.23050
  trust: 0.95
---

## Summary

Unlimited OCR is a document-reading model from Baidu. OCR, optical character recognition, means turning an image of a page into text. The accompanying paper takes DeepSeek OCR as its baseline and says the model can transcribe dozens of pages of documents in a single forward pass under a standard maximum length of 32K.

## Intended Use

The card shows parsing single images in two configurations, called gundam and base, and parsing multi-page documents and PDFs, where each PDF page is first converted to an image and only the base configuration is used. The paper's authors add that the same attention mechanism can also apply to speech recognition and translation.

## What Sets It Apart

The paper points out that a language-model decoder keeps a growing store of past attention data, the KV cache, which raises memory use and slows generation as the output gets longer. It replaces every attention layer in the decoder with Reference Sliding Window Attention, which the authors say reduces attention computation and keeps the KV cache constant for the whole decoding process. It keeps the high-compression encoder of DeepSeek OCR.
