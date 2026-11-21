---
id: model/wav2vec2-lg-xlsr-en-speech-emotion-recognition
type: model
slug: wav2vec2-lg-xlsr-en-speech-emotion-recognition
schema_version: 1.1
title: wav2vec2-lg-xlsr-en-speech-emotion-recognition
organization: organization/ehcalabres
license: license/apache-2-0
model_category: audio
tasks:
- task/audio-classification
parameters_active_b: 0.32
total_parameters: 316496520
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  intermediate_size: 4096
  vocabulary_size: 33
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
  downloads_30d: 9950
  likes: 267
  last_modified: '2024-10-24'
  observed_at: '2026-11-21'
  source: https://huggingface.co/ehcalabres/wav2vec2-lg-xlsr-en-speech-emotion-recognition
aliases:
- ehcalabres/wav2vec2-lg-xlsr-en-speech-emotion-recognition
lineage:
  parents: []
  children: []
tags:
- audio-classification
- wav2vec2
- dense
- mha
updated: '2026-11-21'
sources:
- url: https://huggingface.co/ehcalabres/wav2vec2-lg-xlsr-en-speech-emotion-recognition
  title: ehcalabres/wav2vec2-lg-xlsr-en-speech-emotion-recognition — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/ehcalabres/wav2vec2-lg-xlsr-en-speech-emotion-recognition/blob/main/config.json
  title: ehcalabres/wav2vec2-lg-xlsr-en-speech-emotion-recognition — config.json
  trust: 0.9
---


