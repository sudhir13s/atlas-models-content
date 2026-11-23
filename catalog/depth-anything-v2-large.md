---
id: model/depth-anything-v2-large
type: model
slug: depth-anything-v2-large
schema_version: 3.0
title: Depth-Anything-V2-Large
organization: organization/depth-anything
release_date: '2024-06-13'
license_note: cc-by-nc-4.0
model_category: vision
model_kind: transformer-vision
tasks:
- task/depth-estimation
summary: 'Depth Anything V2 Large is a monocular depth estimation model: from one ordinary photo it predicts a depth map, which says for each pixel how far that point is from the camera. The card says it was trained on 595K synthetic labeled images and more than 62 million real unlabeled images.'
what_sets_it_apart: The card compares it with its first version and with models built on Stable Diffusion, an image-generation model. It states that V2 gives more fine-grained details than Depth Anything V1, is more robust than V1 and the Stable Diffusion-based models Marigold and Geowizard, and is about 10 times faster and lighter than the Stable Diffusion-based models.
pipeline:
  source: https://github.com/DepthAnything/Depth-Anything-V2/blob/main/depth_anything_v2/dpt.py
  summary: A DINOv2 vision transformer reads the photo as small patches. The outputs of four of its blocks are turned back into image-like maps at four sizes, merged by a DPT (Dense Prediction Transformer) head, and a few convolutions give one depth value per pixel.
  stages:
  - id: image
    role: input
    title: Image
    details:
    - shorter side resized to 518 pixels
    note: The photo is resized so its shorter side is 518 pixels and both sides are a multiple of 14, then normalised.
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 14 × 14 pixel patches · width 1024
    note: Each 14 by 14 pixel patch becomes one vector; a class token and position embeddings are added.
  - id: block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 16 heads · DINOv2 ViT-L
    frame: Encoder block
    repeat: 24
  - id: reassemble
    role: projector
    title: Reassemble
    details:
    - tokens of blocks 5, 12, 18 and 24
    - to maps at four scales
    note: The token sequences of four blocks are put back on the patch grid, projected by 1 × 1 convolutions and resized up or down, giving four feature maps of different resolution.
  - id: fusion
    role: feedForward
    title: Fusion
    details:
    - merges the four maps · width 256
    note: Four fusion blocks combine the maps step by step, from the coarsest to the finest, into one feature map.
  - id: head
    role: outputHead
    title: Depth head
    details:
    - convolutions · up to input size
    note: Convolutions reduce the map to one channel, with an upsampling to the input resolution in between; a ReLU keeps the values non-negative.
  - id: depth
    role: outputHead
    title: Depth map
    details:
    - one relative value per pixel
filters:
  is_gated: false
  pipeline_tag: depth-estimation
  library: depth-anything-v2
hub_signals:
  downloads_30d: 132441
  likes: 191
  last_modified: '2024-07-08'
  observed_at: '2026-11-21'
  source: https://huggingface.co/depth-anything/Depth-Anything-V2-Large
aliases:
- depth-anything/Depth-Anything-V2-Large
lineage:
  parents: []
  children: []
quotes:
  summary: Depth Anything V2 is trained from 595K synthetic labeled images and 62M+ real unlabeled images
  what_sets_it_apart: more efficient (10x faster) and more lightweight than SD-based models
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/depth-anything/Depth-Anything-V2-Large
  title: depth-anything/Depth-Anything-V2-Large — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2406.09414
  title: arXiv:2406.09414
  trust: 0.95
- url: https://github.com/DepthAnything/Depth-Anything-V2/blob/main/depth_anything_v2/dpt.py
  title: 'Depth-Anything-V2/depth_anything_v2/dpt.py: model code (Depth Anything)'
  trust: 0.9
- url: https://github.com/DepthAnything/Depth-Anything-V2/blob/main/depth_anything_v2/dinov2.py
  title: 'Depth-Anything-V2/depth_anything_v2/dinov2.py: encoder code (Depth Anything)'
  trust: 0.9
---


