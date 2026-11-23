---
id: model/depth-anything-v2-large
type: model
slug: depth-anything-v2-large
schema_version: 1.1
title: Depth-Anything-V2-Large
organization: organization/depth-anything
release_date: '2024-06-13'
release_year: 2024
release_month: 6
license_note: cc-by-nc-4.0
model_category: vision
tasks:
- task/depth-estimation
summary: 'Depth Anything V2 Large is a monocular depth estimation model: from one ordinary photo it predicts a depth map, which says for each pixel how far that point is from the camera. The card says it was trained on 595K synthetic labeled images and more than 62 million real unlabeled images.'
what_sets_it_apart: The card compares it with its first version and with models built on Stable Diffusion, an image-generation model. It states that V2 gives more fine-grained details than Depth Anything V1, is more robust than V1 and the Stable Diffusion-based models Marigold and Geowizard, and is about 10 times faster and lighter than the Stable Diffusion-based models.
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
tags:
- depth-estimation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/depth-anything/Depth-Anything-V2-Large
  title: depth-anything/Depth-Anything-V2-Large — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2406.09414
  title: arXiv:2406.09414
  trust: 0.95
---

## Summary

Depth Anything V2 Large is a monocular depth estimation model: from one ordinary photo it predicts a depth map, which says for each pixel how far that point is from the camera. The card says it was trained on 595K synthetic labeled images and more than 62 million real unlabeled images.

## What Sets It Apart

The card compares it with its first version and with models built on Stable Diffusion, an image-generation model. It states that V2 gives more fine-grained details than Depth Anything V1, is more robust than V1 and the Stable Diffusion-based models Marigold and Geowizard, and is about 10 times faster and lighter than the Stable Diffusion-based models.
