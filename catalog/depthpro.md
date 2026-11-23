---
id: model/depthpro
type: model
slug: depthpro
schema_version: 3.0
title: DepthPro
organization: organization/apple
release_date: '2024-10-03'
license: license/apple-amlr
model_category: vision
model_kind: transformer-vision
tasks:
- task/depth-estimation
summary: Depth Pro is a depth estimation model from Apple. Given one ordinary photo, it predicts how far each point in the scene is from the camera, producing a depth map. The card says the predictions are metric, meaning they have absolute scale, without needing camera metadata, and that it produces a 2.25-megapixel depth map in 0.3 seconds on a standard GPU.
intended_use: 'The card presents it as a foundation model for zero-shot metric monocular depth estimation: estimating real-world depth from a single image, on images it was not trained on, without extra training. Besides the depth map, the example code returns an estimate of the camera focal length in pixels.'
what_sets_it_apart: 'The card lists these technical contributions: an efficient multi-scale vision transformer for dense prediction, a training protocol that combines real and synthetic datasets, evaluation metrics designed for how well depth maps trace object boundaries, and focal length estimation from a single image. It says the predictions do not depend on camera intrinsics, the settings of the camera that took the picture.'
limitations: The card says the checkpoint in this repository is a reference implementation that has been re-trained, so its performance is close to the model reported in the paper but does not match it exactly.
pipeline:
  source: https://arxiv.org/abs/2410.02073
  summary: The photo is resized to 1536 × 1536 pixels and cut into 384-pixel patches at three scales. One shared vision transformer (ViT) encodes every patch, a second one sees the whole image at low resolution (left), and a decoder merges everything into a depth map that the camera's focal length turns into metres. A field-of-view head (right) estimates the focal length when the photo does not give it.
  side_into: merge
  branch_from: decoder
  stages:
  - id: global-image
    role: input
    title: Whole image
    details:
    - downsampled to 384 × 384
    side: true
  - id: image-encoder
    role: visionEncoder
    title: Image encoder
    details:
    - ViT-L · DINOv2 preset
    note: A separate vision transformer reads the whole image at low resolution and gives global context to the patch features.
    side: true
  - id: image
    role: input
    title: Image
    details:
    - resized to 1536 × 1536
  - id: pyramid
    role: embedding
    title: Patches at three scales
    details:
    - 25 + 9 + 1 = 35 patches of 384 × 384
    note: The image is kept at 1536, 768 and 384 pixels. The two larger copies are cut into overlapping 384-pixel patches (5 × 5 and 3 × 3); the smallest is one patch.
  - id: patch-encoder
    role: visionEncoder
    title: Patch encoder
    details:
    - ViT-L · weights shared across scales
    note: One vision transformer encodes all 35 patches as one batch, giving a 24 × 24 feature grid per patch. At the finest scale, features from intermediate blocks are taken as well.
  - id: merge
    role: projector
    title: Merge into feature maps
    details:
    - patches stitched back per scale
    note: The patch features of each scale are stitched back into one map, upsampled, and the coarsest map is joined with the image encoder's features.
  - id: decoder
    role: feedForward
    title: Decoder
    details:
    - DPT-style fusion · width 256
    note: Convolution blocks fuse the maps from coarse to fine, as in the DPT (Dense Prediction Transformer) decoder.
  - id: head
    role: outputHead
    title: Depth head
    details:
    - canonical inverse depth
    note: Convolutions give a canonical inverse depth map, a depth that does not yet know the camera's field of view.
  - id: metric
    role: outputHead
    title: Depth in metres
    details:
    - scaled by the focal length
    note: The inverse depth is scaled by the focal length in pixels and the image width, then inverted. The focal length comes from the photo's metadata or, if missing, from the field-of-view head.
  - id: fov-head
    role: outputHead
    title: Field-of-view head
    details:
    - own ViT image encoder
    - focal length if metadata lacks it
    note: Reads the decoder's features together with its own ViT image encoder and estimates the camera's field of view, which gives the focal length when the photo's metadata has none.
    branch: true
filters:
  is_gated: false
  pipeline_tag: depth-estimation
  library: depth-pro
hub_signals:
  downloads_30d: 7621
  likes: 569
  last_modified: '2025-02-28'
  observed_at: '2026-11-21'
  source: https://huggingface.co/apple/DepthPro
aliases:
- apple/DepthPro
lineage:
  parents: []
  children: []
quotes:
  summary: a foundation model for zero-shot metric monocular depth estimation
  intended_use: without relying on the availability of metadata such as camera intrinsics
  what_sets_it_apart: an efficient multi-scale vision transformer for dense prediction
  limitations: Its performance is close to the model reported in the paper but does not match it exactly.
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/apple/DepthPro
  title: apple/DepthPro — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2410.02073
  title: arXiv:2410.02073
  trust: 0.95
- url: https://github.com/apple/ml-depth-pro/blob/main/src/depth_pro/network/encoder.py
  title: 'ml-depth-pro/src/depth_pro/network/encoder.py: multi-scale encoder code (Apple)'
  trust: 0.9
- url: https://github.com/apple/ml-depth-pro/blob/main/src/depth_pro/depth_pro.py
  title: 'ml-depth-pro/src/depth_pro/depth_pro.py: model code (Apple)'
  trust: 0.9
---


