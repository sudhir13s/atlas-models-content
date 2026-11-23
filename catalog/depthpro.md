---
id: model/depthpro
type: model
slug: depthpro
schema_version: 1.1
title: DepthPro
organization: organization/apple
release_date: '2024-10-03'
release_year: 2024
release_month: 10
license: license/apple-amlr
model_category: vision
tasks:
- task/depth-estimation
summary: Depth Pro is a depth estimation model from Apple. Given one ordinary photo, it predicts how far each point in the scene is from the camera, producing a depth map. The card says the predictions are metric, meaning they have absolute scale, without needing camera metadata, and that it produces a 2.25-megapixel depth map in 0.3 seconds on a standard GPU.
intended_use: 'The card presents it as a foundation model for zero-shot metric monocular depth estimation: estimating real-world depth from a single image, on images it was not trained on, without extra training. Besides the depth map, the example code returns an estimate of the camera focal length in pixels.'
what_sets_it_apart: 'The card lists these technical contributions: an efficient multi-scale vision transformer for dense prediction, a training protocol that combines real and synthetic datasets, evaluation metrics designed for how well depth maps trace object boundaries, and focal length estimation from a single image. It says the predictions do not depend on camera intrinsics, the settings of the camera that took the picture.'
limitations: The card says the checkpoint in this repository is a reference implementation that has been re-trained, so its performance is close to the model reported in the paper but does not match it exactly.
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
tags:
- depth-estimation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/apple/DepthPro
  title: apple/DepthPro — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2410.02073
  title: arXiv:2410.02073
  trust: 0.95
---

## Summary

Depth Pro is a depth estimation model from Apple. Given one ordinary photo, it predicts how far each point in the scene is from the camera, producing a depth map. The card says the predictions are metric, meaning they have absolute scale, without needing camera metadata, and that it produces a 2.25-megapixel depth map in 0.3 seconds on a standard GPU.

## Intended Use

The card presents it as a foundation model for zero-shot metric monocular depth estimation: estimating real-world depth from a single image, on images it was not trained on, without extra training. Besides the depth map, the example code returns an estimate of the camera focal length in pixels.

## What Sets It Apart

The card lists these technical contributions: an efficient multi-scale vision transformer for dense prediction, a training protocol that combines real and synthetic datasets, evaluation metrics designed for how well depth maps trace object boundaries, and focal length estimation from a single image. It says the predictions do not depend on camera intrinsics, the settings of the camera that took the picture.

## Limitations

The card says the checkpoint in this repository is a reference implementation that has been re-trained, so its performance is close to the model reported in the paper but does not match it exactly.
