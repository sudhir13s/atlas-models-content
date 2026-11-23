---
id: model/dpt-large
type: model
slug: dpt-large
schema_version: 3.0
title: dpt-large
organization: organization/intel
license: license/apache-2-0
model_category: vision
model_kind: transformer-vision
tasks:
- task/depth-estimation
parameters_active_b: 0.34
total_parameters: 341850305
summary: DPT-Large, also known as MiDaS 3.0, is a depth estimation model from Intel. From a single ordinary photo it predicts how far away each part of the scene is, a task called monocular depth estimation. The card says it is a Dense Prediction Transformer trained on 1.4 million images.
intended_use: The card lists zero-shot monocular depth estimation as the primary use, for anyone doing that task. It says that in most cases the model will need to be fine-tuned for a particular task, and that it should not be used to intentionally create hostile or alienating environments for people.
what_sets_it_apart: The card says DPT uses a Vision Transformer (ViT) as its backbone and adds a neck and a head on top to produce the depth map.
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  intermediate_size: 4096
  activation_function: gelu
  normalization: LayerNorm
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/dpt/modeling_dpt.py
  summary: A vision transformer reads the image as patches. The outputs of four of its blocks are turned back into image-like maps at four sizes, merged, and a small convolutional head gives one depth value per pixel.
  stages:
  - id: image
    role: input
    title: Image
    details:
    - 384 × 384 pixels
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 16 × 16 pixel patches · width 1024
    note: Each patch becomes one vector; a class token and position embeddings are added.
  - id: block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 16 heads
    frame: Encoder block
    repeat: 24
  - id: reassemble
    role: projector
    title: Reassemble
    details:
    - tokens of blocks 6, 12, 18 and 24
    - to maps at four scales
    note: The token sequences of four blocks are put back on the image grid and resized, giving four feature maps of different resolution.
  - id: fusion
    role: step
    title: Fusion
    details:
    - merges the four maps into one
    note: Convolution blocks combine the four maps step by step into one detailed feature map.
  - id: head
    role: outputHead
    title: Depth head
    details:
    - convolution layers
  - id: depth
    role: outputHead
    title: Depth map
    details:
    - one value per pixel
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: depth-estimation
  library: transformers
hub_signals:
  downloads_30d: 27715
  likes: 208
  last_modified: '2024-02-24'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Intel/dpt-large
aliases:
- Intel/dpt-large
lineage:
  parents: []
  children: []
quotes:
  summary: Dense Prediction Transformer (DPT) model trained on 1.4 million images for monocular depth estimation.
  intended_use: This model in most cases will need to be fine-tuned for your particular task.
  what_sets_it_apart: DPT uses the Vision Transformer (ViT) as backbone and adds a neck + head on top for monocular depth estimation.
tags:
- dpt
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Intel/dpt-large
  title: Intel/dpt-large — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Intel/dpt-large/blob/main/config.json
  title: Intel/dpt-large — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2103.13413
  title: arXiv:2103.13413
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/dpt/modeling_dpt.py
  title: 'transformers/models/dpt/modeling_dpt.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


