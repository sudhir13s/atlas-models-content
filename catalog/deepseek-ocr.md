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
tags:
- image-text-to-text
- deepseek-vl-v2
- moe
- mla
updated: '2026-11-21'
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


