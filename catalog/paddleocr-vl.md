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
tags:
- image-text-to-text
- paddleocr-vl
- dense
- gqa
updated: '2026-11-21'
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


