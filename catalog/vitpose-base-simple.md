---
id: model/vitpose-base-simple
type: model
slug: vitpose-base-simple
schema_version: 1.1
title: vitpose-base-simple
organization: organization/usyd-community
release_date: '2025-01-08'
release_year: 2025
release_month: 1
license: license/apache-2-0
model_category: vision
tasks:
- task/keypoint-detection
parameters_active_b: 0.09
total_parameters: 85912337
summary: ViTPose is a human pose estimation model, which finds the positions of body joints such as the head, shoulders, elbows, wrists, hips, knees and ankles in an image. This is the base-size checkpoint, ported to the Hugging Face Transformers library. The card says ViTPose uses a plain vision transformer to extract features for a given person and a lightweight decoder to estimate the pose.
intended_use: The card says the model is primarily designed for pose estimation tasks, such as estimating the poses of humans in images or videos by locating key body joints.
what_sets_it_apart: The paper abstract in the card says ViTPose uses plain, non-hierarchical vision transformers with no pose-specific design, can be scaled from 100 million to 1 billion parameters, and can transfer the knowledge of large models to small ones through a simple knowledge token.
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vitpose/modeling_vitpose.py
  summary: ViTPose works top-down. A separate person detector finds each person, and the crop around one person is read by a plain vision transformer. A light decoder turns the patch features into one heatmap per body keypoint, and the peak of each heatmap is the keypoint's position.
  stages:
  - id: crop
    role: input
    title: Person crop
    details:
    - 256 × 192 pixels
    note: The card's example finds people with a separate detector (RT-DETR) and passes their boxes; each box is cropped and resized.
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 16 × 16 pixel patches · width 768
    note: A convolution turns each patch into one vector and learned position embeddings are added; there is no class token in the sequence.
  - id: block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 12 heads · norm before each part
    frame: Encoder block
    repeat: 12
  - id: norm
    role: normalisation
    title: Final LayerNorm
    details:
    - back to a 16 × 12 feature map
  - id: decoder
    role: feedForward
    title: Simple decoder
    details:
    - ReLU · 4× bilinear upsampling
    - 3 × 3 convolution
    note: The feature map is passed through a ReLU, upsampled four times and turned by one convolution into one channel per keypoint.
  - id: heatmaps
    role: outputHead
    title: Keypoint heatmaps
    details:
    - 17 COCO keypoints · 64 × 48
    note: Each map is high where its keypoint (nose, eyes, ears, shoulders, elbows, wrists, hips, knees, ankles) is likely. Post-processing reads the peak of each map back into image coordinates.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: keypoint-detection
  library: transformers
hub_signals:
  downloads_30d: 320052
  likes: 35
  last_modified: '2025-01-08'
  observed_at: '2026-11-21'
  source: https://huggingface.co/usyd-community/vitpose-base-simple
aliases:
- usyd-community/vitpose-base-simple
lineage:
  parents: []
  children: []
quotes:
  summary: ViTPose employs plain and non-hierarchical vision transformers as backbones to extract features for a given person instance and a lightweight decoder for pose estimation.
  intended_use: 'Human Pose Estimation: The model can be used to estimate the poses of humans in images or videos.'
  what_sets_it_apart: It can be scaled up from 100M to 1B parameters by taking the advantages of the scalable model capacity and high parallelism of transformers
tags:
- keypoint-detection
- vitpose
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/usyd-community/vitpose-base-simple
  title: usyd-community/vitpose-base-simple — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/usyd-community/vitpose-base-simple/blob/main/config.json
  title: usyd-community/vitpose-base-simple — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2204.12484
  title: arXiv:2204.12484
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vitpose/modeling_vitpose.py
  title: 'transformers/models/vitpose/modeling_vitpose.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vitpose_backbone/configuration_vitpose_backbone.py
  title: 'transformers/models/vitpose_backbone/configuration_vitpose_backbone.py: backbone defaults (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

ViTPose is a human pose estimation model, which finds the positions of body joints such as the head, shoulders, elbows, wrists, hips, knees and ankles in an image. This is the base-size checkpoint, ported to the Hugging Face Transformers library. The card says ViTPose uses a plain vision transformer to extract features for a given person and a lightweight decoder to estimate the pose.

## Intended Use

The card says the model is primarily designed for pose estimation tasks, such as estimating the poses of humans in images or videos by locating key body joints.

## What Sets It Apart

The paper abstract in the card says ViTPose uses plain, non-hierarchical vision transformers with no pose-specific design, can be scaled from 100 million to 1 billion parameters, and can transfer the knowledge of large models to small ones through a simple knowledge token.
