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
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vjepa2/modeling_vjepa2.py
  summary: 'A 64-frame clip is cut into small space-time blocks and a vision transformer turns them into one feature vector per block; these are the video features the card uses. The checkpoint also holds the predictor used in pre-training: from the features of the visible blocks it predicts the features of hidden ones.'
  stages:
  - id: video
    role: input
    title: Video clip
    details:
    - 64 frames · 256 × 256 pixels
  - id: tubelets
    role: embedding
    title: Tubelet embedding
    details:
    - 2 frames × 16 × 16 pixels · 1024
    note: A 3D convolution turns each block of 2 frames by 16 by 16 pixels into one vector, giving 32 × 16 × 16 = 8,192 vectors per clip.
  - id: encoder-block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 16 heads · 3D rotary positions
    note: Positions in time, height and width are given by rotary embeddings inside attention; there is no added position embedding.
    frame: Encoder block
    repeat: 24
  - id: encoder-norm
    role: normalisation
    title: Final LayerNorm
    details:
    - video features · width 1024
    note: This output is what the card's get_vision_features example returns, for classification, retrieval or as a video encoder.
  - id: predictor-input
    role: projector
    title: Visible + mask tokens
    details:
    - width 1024 → 384
    note: In pre-training, the features of the visible blocks are mapped to width 384 and learned mask tokens are added at the positions to predict.
  - id: predictor-block
    role: attention
    title: Attention + feed-forward
    details:
    - 12 heads · width 384
    frame: Predictor block
    repeat: 12
  - id: predictor-out
    role: outputHead
    title: Predicted features
    details:
    - LayerNorm · width 384 → 1024
    note: The outputs at the mask positions are normalised and mapped back to width 1024, as predictions of the hidden blocks' features.
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
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vjepa2/modeling_vjepa2.py
  title: 'transformers/models/vjepa2/modeling_vjepa2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

V-JEPA 2 is a video understanding model developed by FAIR at Meta. The card says it extends the pre-training objectives of the earlier V-JEPA model, and describes it as a model that represents videos and images as features for tasks such as classification and retrieval.

## Intended Use

The card says V-JEPA 2 is intended to represent any video or image, in order to do video classification and retrieval or to act as a video encoder for vision-language models (VLMs).

## Running It

The card says to sample the number of frames according to the model, and that this checkpoint uses 64 frames. It asks for the latest Transformers, installed from the GitHub repository.
