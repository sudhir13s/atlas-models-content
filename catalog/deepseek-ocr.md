---
id: model/deepseek-ocr
type: model
slug: deepseek-ocr
schema_version: 1.1
title: DeepSeek-OCR
organization: organization/deepseek
release_date: '2025-10-17'
release_year: 2025
release_month: 10
license: license/mit
model_category: documents
tasks:
- task/ocr
- task/vision-language-models
total_parameters: 3336106240
context_window_tokens: 8192
summary: DeepSeek-OCR is a model from DeepSeek-AI that reads an image of a document and writes out the text it contains, a task called optical character recognition (OCR). It is a vision-language model, meaning it takes an image and a text prompt and replies in text. Its paper presents it as an initial investigation into compressing long text into images, so that fewer tokens (the small pieces a model reads) are needed.
intended_use: The card shows it reading text from images, either as plain text (the prompt "Free OCR") or by converting a document page to Markdown. The paper adds that it can generate training data for language models and vision-language models, and names research on compressing long contexts as a direction.
what_sets_it_apart: The paper describes two parts. DeepEncoder turns a high-resolution page into a small, manageable number of vision tokens, and the decoder is named DeepSeek3B-MoE-A570M (MoE stands for mixture of experts). The authors report that the model decodes text well when the text has up to 10 times as many tokens as the image does, and that accuracy drops at a 20-times ratio.
running_it: 'The card shows inference with Hugging Face Transformers on NVIDIA GPUs, with pinned package versions and flash-attn installed. It lists five input-size modes set through the base_size, image_size and crop_mode settings: Tiny, Small, Base, Large and Gundam.'
fingerprint:
  attention_type: MLA
  num_layers: 12
  attention_heads: 10
  kv_heads: 10
  head_size: 128
  hidden_size: 1280
  intermediate_size: 6848
  context_window_tokens: 8192
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
  downloads_30d: 1799082
  likes: 3467
  last_modified: '2025-11-04'
  observed_at: '2026-11-21'
  source: https://huggingface.co/deepseek-ai/DeepSeek-OCR
aliases:
- deepseek-ai/DeepSeek-OCR
lineage:
  parents: []
  children: []
quotes:
  summary: initial investigation into the feasibility of compressing long contexts via optical 2D mapping
  intended_use: Convert the document to markdown.
  what_sets_it_apart: DeepEncoder serves as the core engine, designed to maintain low activations under high-resolution input while achieving high compression ratios
  running_it: Inference using Huggingface transformers on NVIDIA GPUs.
tags:
- image-text-to-text
- deepseek-vl-v2
- moe
- mla
updated: '2026-11-23'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-OCR
  title: deepseek-ai/DeepSeek-OCR — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-OCR/blob/main/config.json
  title: deepseek-ai/DeepSeek-OCR — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2510.18234
  title: arXiv:2510.18234
  trust: 0.95
---

## Summary

DeepSeek-OCR is a model from DeepSeek-AI that reads an image of a document and writes out the text it contains, a task called optical character recognition (OCR). It is a vision-language model, meaning it takes an image and a text prompt and replies in text. Its paper presents it as an initial investigation into compressing long text into images, so that fewer tokens (the small pieces a model reads) are needed.

## Intended Use

The card shows it reading text from images, either as plain text (the prompt "Free OCR") or by converting a document page to Markdown. The paper adds that it can generate training data for language models and vision-language models, and names research on compressing long contexts as a direction.

## What Sets It Apart

The paper describes two parts. DeepEncoder turns a high-resolution page into a small, manageable number of vision tokens, and the decoder is named DeepSeek3B-MoE-A570M (MoE stands for mixture of experts). The authors report that the model decodes text well when the text has up to 10 times as many tokens as the image does, and that accuracy drops at a 20-times ratio.

## Running It

The card shows inference with Hugging Face Transformers on NVIDIA GPUs, with pinned package versions and flash-attn installed. It lists five input-size modes set through the base_size, image_size and crop_mode settings: Tiny, Small, Base, Large and Gundam.
