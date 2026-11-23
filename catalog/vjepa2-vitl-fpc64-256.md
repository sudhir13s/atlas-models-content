---
id: model/vjepa2-vitl-fpc64-256
type: model
slug: vjepa2-vitl-fpc64-256
schema_version: 1.1
title: vjepa2-vitl-fpc64-256
organization: organization/meta
release_date: '2025-05-31'
release_year: 2025
release_month: 5
license: license/mit
model_category: vision
tasks:
- task/video-understanding
parameters_active_b: 0.33
total_parameters: 325971328
summary: V-JEPA 2 is a video understanding model developed by FAIR at Meta. The card says it extends the pre-training objectives of the earlier V-JEPA model, and describes it as a model that represents videos and images as features for tasks such as classification and retrieval.
intended_use: The card says V-JEPA 2 is intended to represent any video or image, in order to do video classification and retrieval or to act as a video encoder for vision-language models (VLMs).
running_it: The card says to sample the number of frames according to the model, and that this checkpoint uses 64 frames. It asks for the latest Transformers, installed from the GitHub repository.
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  activation_function: gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: video-classification
  library: transformers
hub_signals:
  downloads_30d: 370472
  likes: 222
  last_modified: '2025-08-11'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/vjepa2-vitl-fpc64-256
aliases:
- facebook/vjepa2-vitl-fpc64-256
lineage:
  parents: []
  children: []
quotes:
  summary: developed by FAIR, Meta
  intended_use: V-JEPA 2 is intended to represent any video (and image) to perform video classification, retrieval, or as a video encoder for VLMs.
  running_it: To load a video, sample the number of frames according to the model. For this model, we use 64.
tags:
- video-classification
- vjepa2
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/vjepa2-vitl-fpc64-256
  title: facebook/vjepa2-vitl-fpc64-256 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/vjepa2-vitl-fpc64-256/blob/main/config.json
  title: facebook/vjepa2-vitl-fpc64-256 — config.json
  trust: 0.9
---

## Summary

V-JEPA 2 is a video understanding model developed by FAIR at Meta. The card says it extends the pre-training objectives of the earlier V-JEPA model, and describes it as a model that represents videos and images as features for tasks such as classification and retrieval.

## Intended Use

The card says V-JEPA 2 is intended to represent any video or image, in order to do video classification and retrieval or to act as a video encoder for vision-language models (VLMs).

## Running It

The card says to sample the number of frames according to the model, and that this checkpoint uses 64 frames. It asks for the latest Transformers, installed from the GitHub repository.
