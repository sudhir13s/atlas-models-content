---
id: model/sam3
type: model
slug: sam3
schema_version: 1.1
title: sam3
organization: organization/meta
release_date: '2025-11-07'
release_year: 2025
release_month: 11
license_note: other
model_category: vision
tasks:
- task/image-segmentation
total_parameters: 859922360
summary: SAM 3 is an image and video segmentation model published on Hugging Face under the facebook organization. Segmentation means outlining the exact pixels of an object. The card says it can detect, segment and track objects in images and videos using text prompts or visual prompts such as points, boxes and masks.
what_sets_it_apart: The card says that, compared with its predecessor SAM 2, SAM 3 can segment every instance of an open-vocabulary concept, meaning any concept named by a short text phrase or shown by example images. The card says it handles a much larger set of such prompts than earlier work.
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/sam3/modeling_sam3.py
  summary: The image detector of SAM 3 as Transformers implements it. A vision transformer turns the image into feature maps, a text encoder (left) turns the prompt into vectors, and a fusion encoder mixes the two. A DETR-style decoder (DEtection TRansformer) turns 200 learned queries into boxes and scores, and a mask decoder (right) gives each query a mask.
  side_into: fusion
  branch_from: decoder
  stages:
  - id: prompt
    role: input
    title: Text prompt
    details:
    - a short noun phrase
    side: true
  - id: text-encoder
    role: textEncoder
    title: CLIP text encoder
    details:
    - 24 layers · width 1024
    note: A CLIP-style text transformer turns the prompt into one vector per token; a linear layer maps them to width 256. Optional prompt boxes are encoded by a small geometry encoder and joined to these vectors.
    side: true
  - id: image
    role: input
    title: Image
    details:
    - 1008 × 1008 pixels
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 14 × 14 pixel patches · width 1024
  - id: vision-block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 16 heads · windowed, 4 layers global
    note: Most layers attend within windows of 24 × 24 patches; layers 8, 16, 24 and 32 attend over the whole image. Positions use rotary embeddings.
    frame: Vision block
    repeat: 32
  - id: neck
    role: projector
    title: Feature pyramid
    details:
    - maps at 288, 144 and 72 · width 256
    note: The 72 × 72 patch grid is scaled up and down into feature maps of several sizes for the later stages.
  - id: fusion
    role: attention
    title: Fusion encoder
    details:
    - 6 layers · image attends to prompt
    note: Each layer runs self-attention over the image features, cross-attention from the image features to the prompt vectors, and a feed-forward network.
  - id: decoder
    role: attention
    title: Query decoder
    details:
    - 6 layers · 200 queries
    note: Learned object queries attend to each other, to the prompt and to the image features. Each layer refines one box per query; a presence token scores whether the concept is in the image at all.
  - id: scores
    role: outputHead
    title: Boxes + scores
    details:
    - score = query match × presence
    note: Each query's score is a dot product with the prompt vectors, multiplied by the presence score.
  - id: masks
    role: outputHead
    title: Mask decoder
    details:
    - one mask per query
    note: A pixel decoder upsamples the feature maps; each query's embedding from the query decoder is multiplied with the pixel embeddings to give its mask. A semantic mask for the whole prompt is also produced.
    branch: true
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: mask-generation
  library: transformers
hub_signals:
  downloads_30d: 1895035
  likes: 7148
  last_modified: '2025-11-20'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/sam3
aliases:
- facebook/sam3
lineage:
  parents: []
  children: []
quotes:
  summary: SAM 3 is a unified foundation model for promptable segmentation in images and videos.
  what_sets_it_apart: SAM 3 introduces the ability to exhaustively segment all instances of an open-vocabulary concept specified by a short text phrase or exemplars.
tags:
- mask-generation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/sam3
  title: facebook/sam3 — model page (Hugging Face)
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/sam3/modeling_sam3.py
  title: 'transformers/models/sam3/modeling_sam3.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/sam3/configuration_sam3.py
  title: 'transformers/models/sam3/configuration_sam3.py: default configuration for facebook/sam3 (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

SAM 3 is an image and video segmentation model published on Hugging Face under the facebook organization. Segmentation means outlining the exact pixels of an object. The card says it can detect, segment and track objects in images and videos using text prompts or visual prompts such as points, boxes and masks.

## What Sets It Apart

The card says that, compared with its predecessor SAM 2, SAM 3 can segment every instance of an open-vocabulary concept, meaning any concept named by a short text phrase or shown by example images. The card says it handles a much larger set of such prompts than earlier work.
