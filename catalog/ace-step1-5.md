---
id: model/ace-step1-5
type: model
slug: ace-step1-5
schema_version: 1.1
title: Ace-Step1.5
organization: organization/ace-step
release_date: '2026-01-23'
release_year: 2026
release_month: 1
license: license/mit
model_category: generation
tasks:
- task/music-and-sound-generation
context_window_tokens: 32768
fingerprint:
  attention_type: GQA
  num_layers: 24
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  context_window_tokens: 32768
  vocabulary_size: 64003
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-to-audio
  library: transformers
hub_signals:
  downloads_30d: 105775
  likes: 986
  last_modified: '2026-02-03'
  observed_at: '2026-11-21'
  source: https://huggingface.co/ACE-Step/Ace-Step1.5
aliases:
- ACE-Step/Ace-Step1.5
lineage:
  parents: []
  children: []
tags:
- text-to-audio
- acestep
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/ACE-Step/Ace-Step1.5
  title: ACE-Step/Ace-Step1.5 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/ACE-Step/Ace-Step1.5/blob/main/config.json
  title: ACE-Step/Ace-Step1.5 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2602.00744
  title: arXiv:2602.00744
  trust: 0.95
---


