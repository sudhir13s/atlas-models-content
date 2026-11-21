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
tags:
- image-text-to-text
- unlimited-ocr
- moe
- mla
updated: '2026-11-21'
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


