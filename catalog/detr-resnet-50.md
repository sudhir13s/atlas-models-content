---
id: model/detr-resnet-50
type: model
slug: detr-resnet-50
schema_version: 1.1
title: detr-resnet-50
organization: organization/meta
license: license/apache-2-0
model_category: vision
tasks:
- task/object-detection
parameters_active_b: 0.04
total_parameters: 41631008
context_window_tokens: 1024
summary: 'DETR (DEtection TRansformer) with a ResNet-50 backbone is an object detection model: given an image, it outputs a box and a class label for each object it finds. It was introduced in the paper by Carion et al. and first released in the facebookresearch repository, and the card says it was trained on COCO 2017, a set of 118 thousand annotated images. The Hugging Face team wrote the card, because the releasing team did not.'
intended_use: The card says the raw model can be used for object detection and points to the model hub for other DETR models.
what_sets_it_apart: 'The card describes an encoder-decoder transformer on top of a convolutional backbone, which uses 100 object queries, each of which looks for one particular object in the image. Training uses a bipartite matching loss: the Hungarian algorithm pairs each query with one ground-truth annotation, so that every object is matched exactly once.'
fingerprint:
  num_layers: 6
  hidden_size: 256
  context_window_tokens: 1024
  activation_function: relu
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: object-detection
  library: transformers
hub_signals:
  downloads_30d: 113423
  likes: 991
  last_modified: '2024-04-10'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/detr-resnet-50
aliases:
- facebook/detr-resnet-50
lineage:
  parents: []
  children: []
quotes:
  summary: DEtection TRansformer (DETR) model trained end-to-end on COCO 2017 object detection (118k annotated images).
  intended_use: You can use the raw model for object detection.
  what_sets_it_apart: Each object query looks for a particular object in the image.
tags:
- object-detection
- detr
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/detr-resnet-50
  title: facebook/detr-resnet-50 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/detr-resnet-50/blob/main/config.json
  title: facebook/detr-resnet-50 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2005.12872
  title: arXiv:2005.12872
  trust: 0.95
---

## Summary

DETR (DEtection TRansformer) with a ResNet-50 backbone is an object detection model: given an image, it outputs a box and a class label for each object it finds. It was introduced in the paper by Carion et al. and first released in the facebookresearch repository, and the card says it was trained on COCO 2017, a set of 118 thousand annotated images. The Hugging Face team wrote the card, because the releasing team did not.

## Intended Use

The card says the raw model can be used for object detection and points to the model hub for other DETR models.

## What Sets It Apart

The card describes an encoder-decoder transformer on top of a convolutional backbone, which uses 100 object queries, each of which looks for one particular object in the image. Training uses a bipartite matching loss: the Hungarian algorithm pairs each query with one ground-truth annotation, so that every object is matched exactly once.
