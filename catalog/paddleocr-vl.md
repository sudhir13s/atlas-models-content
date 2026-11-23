---
id: model/paddleocr-vl
type: model
slug: paddleocr-vl
schema_version: 1.1
title: PaddleOCR-VL
organization: organization/paddlepaddle
release_date: '2025-10-16'
release_year: 2025
release_month: 10
license: license/apache-2-0
model_category: documents
tasks:
- task/ocr
- task/vision-language-models
parameters_active_b: 0.96
total_parameters: 958588736
context_window_tokens: 131072
base_models:
- name: baidu/ERNIE-4.5-0.3B-Paddle
  address: https://huggingface.co/baidu/ERNIE-4.5-0.3B-Paddle
summary: 'PaddleOCR-VL is a document parsing model from PaddlePaddle: it reads an image of a page and recognizes its text, tables, formulas and charts. Its core component is PaddleOCR-VL-0.9B, a vision-language model, meaning a model that reads images and writes text, which joins a NaViT-style dynamic-resolution visual encoder with the ERNIE-4.5-0.3B language model. The card states support for 109 languages.'
intended_use: The card presents it for document parsing at page level and for recognizing individual elements such as text, tables, formulas and charts. It says the content types it handles include handwritten text and historical documents.
what_sets_it_apart: 'The card describes a compact design made for resource-efficient inference: a visual encoder that takes images at dynamic high resolution, paired with a lightweight language model, ERNIE-4.5-0.3B. The card lists the 109 languages as including Chinese, English, Japanese, Latin and Korean, as well as Russian (Cyrillic script), Arabic, Hindi (Devanagari script) and Thai.'
fingerprint:
  attention_type: GQA
  num_layers: 18
  attention_heads: 16
  kv_heads: 2
  head_size: 128
  hidden_size: 1024
  intermediate_size: 3072
  context_window_tokens: 131072
  vocabulary_size: 103424
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 500000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: PaddleOCR
hub_signals:
  downloads_30d: 18846
  likes: 1688
  last_modified: '2026-11-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/PaddlePaddle/PaddleOCR-VL
aliases:
- PaddlePaddle/PaddleOCR-VL
lineage:
  parents: []
  children: []
quotes:
  summary: integrates a NaViT-style dynamic resolution visual encoder with the ERNIE-4.5-0.3B language model
  intended_use: model tailored for document parsing
  what_sets_it_apart: specifically designed for resource-efficient inference
tags:
- image-text-to-text
- paddleocr-vl
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/PaddlePaddle/PaddleOCR-VL
  title: PaddlePaddle/PaddleOCR-VL — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/PaddlePaddle/PaddleOCR-VL/blob/main/config.json
  title: PaddlePaddle/PaddleOCR-VL — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2510.14528
  title: arXiv:2510.14528
  trust: 0.95
---

## Summary

PaddleOCR-VL is a document parsing model from PaddlePaddle: it reads an image of a page and recognizes its text, tables, formulas and charts. Its core component is PaddleOCR-VL-0.9B, a vision-language model, meaning a model that reads images and writes text, which joins a NaViT-style dynamic-resolution visual encoder with the ERNIE-4.5-0.3B language model. The card states support for 109 languages.

## Intended Use

The card presents it for document parsing at page level and for recognizing individual elements such as text, tables, formulas and charts. It says the content types it handles include handwritten text and historical documents.

## What Sets It Apart

The card describes a compact design made for resource-efficient inference: a visual encoder that takes images at dynamic high resolution, paired with a lightweight language model, ERNIE-4.5-0.3B. The card lists the 109 languages as including Chinese, English, Japanese, Latin and Korean, as well as Russian (Cyrillic script), Arabic, Hindi (Devanagari script) and Thai.
