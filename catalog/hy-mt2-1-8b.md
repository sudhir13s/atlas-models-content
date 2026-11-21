---
id: model/hy-mt2-1-8b
type: model
slug: hy-mt2-1-8b
schema_version: 1.1
title: Hy-MT2-1.8B
organization: organization/tencent
release_date: '2026-05-11'
release_year: 2026
release_month: 5
license: license/apache-2-0
model_category: language
tasks:
- task/translation
parameters_active_b: 2.04
total_parameters: 2038515712
context_window_tokens: 262144
fingerprint:
  attention_type: GQA
  num_layers: 32
  attention_heads: 16
  kv_heads: 4
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  context_window_tokens: 262144
  vocabulary_size: 120818
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: translation
  library: transformers
hub_signals:
  downloads_30d: 21419
  likes: 1294
  last_modified: '2026-05-26'
  observed_at: '2026-11-21'
  source: https://huggingface.co/tencent/Hy-MT2-1.8B
aliases:
- tencent/Hy-MT2-1.8B
lineage:
  parents: []
  children: []
tags:
- llm
- translation
- hunyuan-v1-dense
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/tencent/Hy-MT2-1.8B
  title: tencent/Hy-MT2-1.8B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/tencent/Hy-MT2-1.8B/blob/main/config.json
  title: tencent/Hy-MT2-1.8B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2605.22064
  title: arXiv:2605.22064
  trust: 0.95
---


