---
id: model/moss-transcribe-diarize
type: model
slug: moss-transcribe-diarize
schema_version: 1.1
title: MOSS-Transcribe-Diarize
organization: organization/openmoss-team
release_date: '2026-05-19'
release_year: 2026
release_month: 5
license: license/apache-2-0
model_category: audio
tasks:
- task/speech-language-models
parameters_active_b: 0.91
total_parameters: 908513280
context_window_tokens: 131072
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 1024
  intermediate_size: 3072
  context_window_tokens: 131072
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: audio-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 148877
  likes: 529
  last_modified: '2026-10-31'
  observed_at: '2026-11-21'
  source: https://huggingface.co/OpenMOSS-Team/MOSS-Transcribe-Diarize
aliases:
- OpenMOSS-Team/MOSS-Transcribe-Diarize
lineage:
  parents: []
  children: []
tags:
- audio-text-to-text
- moss-transcribe-diarize
- dense
- gqa
updated: '2026-11-21'
sources:
- url: https://huggingface.co/OpenMOSS-Team/MOSS-Transcribe-Diarize
  title: OpenMOSS-Team/MOSS-Transcribe-Diarize — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/OpenMOSS-Team/MOSS-Transcribe-Diarize/blob/main/config.json
  title: OpenMOSS-Team/MOSS-Transcribe-Diarize — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2601.01554
  title: arXiv:2601.01554
  trust: 0.95
---


