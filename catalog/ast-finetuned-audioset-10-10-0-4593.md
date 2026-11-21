---
id: model/ast-finetuned-audioset-10-10-0-4593
type: model
slug: ast-finetuned-audioset-10-10-0-4593
schema_version: 1.1
title: ast-finetuned-audioset-10-10-0.4593
organization: organization/mit
release_date: '2022-11-14'
release_year: 2022
release_month: 11
license_note: bsd-3-clause
model_category: audio
tasks:
- task/audio-classification
parameters_active_b: 0.09
total_parameters: 86594063
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  activation_function: gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: audio-classification
  library: transformers
hub_signals:
  downloads_30d: 1940674
  likes: 392
  last_modified: '2023-09-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/MIT/ast-finetuned-audioset-10-10-0.4593
aliases:
- MIT/ast-finetuned-audioset-10-10-0.4593
lineage:
  parents: []
  children: []
tags:
- audio-classification
- audio-spectrogram-transformer
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/MIT/ast-finetuned-audioset-10-10-0.4593
  title: MIT/ast-finetuned-audioset-10-10-0.4593 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/MIT/ast-finetuned-audioset-10-10-0.4593/blob/main/config.json
  title: MIT/ast-finetuned-audioset-10-10-0.4593 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2104.01778
  title: arXiv:2104.01778
  trust: 0.95
---


