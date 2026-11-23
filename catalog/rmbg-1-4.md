---
id: model/rmbg-1-4
type: model
slug: rmbg-1-4
schema_version: 3.0
title: RMBG-1.4
organization: organization/briaai
release_date: '2023-12-12'
license_note: bria-rmbg-1.4
model_category: vision
model_kind: convolutional
tasks:
- task/image-segmentation
parameters_active_b: 0.04
total_parameters: 44075590
summary: 'RMBG v1.4 is a background removal model from BRIA AI: it separates the foreground subject of an image from its background so the background can be cut out. The card calls it a saliency segmentation model, one that finds the most prominent object, trained exclusively on a professional-grade dataset. It is released as a source-available model for non-commercial use.'
intended_use: The card says it is meant to separate foreground from background across a range of image types, and lists general stock images, e-commerce, gaming and advertising content among its training sources. It states that non-commercial use is allowed under the card's license and that commercial use needs a commercial agreement with BRIA.
what_sets_it_apart: BRIA says it is built on IS-Net, an existing segmentation network, with its own training scheme and proprietary dataset. The card says the training set has over 12,000 manually labeled, fully licensed images, and that its benchmark balanced gender and ethnicity and included people with different types of disabilities.
running_it: The card shows use through the Transformers image-segmentation pipeline with trust_remote_code, and a direct-loading example that resizes the input to 1024 by 1024 pixels. It notes that access is gated and that a newer version, RMBG-3.0, exists.
pipeline:
  source: https://huggingface.co/briaai/RMBG-1.4/blob/main/briarmbg.py
  summary: A U-shaped convolutional network, the IS-Net design, reads the photo at 1024 × 1024 pixels. Its encoder shrinks the image in six stages, its decoder grows it back in five, joining in the encoder's map of the same size each time, and the last map becomes a mask that says for each pixel how likely it is foreground.
  stages:
  - id: image
    role: input
    title: Image
    details:
    - resized to 1024 × 1024
  - id: stem
    role: embedding
    title: Input convolution
    details:
    - stride 2 · 64 channels
    note: One 3 × 3 convolution with stride 2 halves the image size and gives 64 feature channels.
  - id: encoder
    role: visionEncoder
    title: Encoder stages
    details:
    - 6 residual U-blocks (RSU)
    - max-pooling between stages
    note: Each stage is a residual U-block, itself a small U-shaped network of convolutions; between stages the map is halved by max-pooling.
  - id: decoder
    role: feedForward
    title: Decoder stages
    details:
    - 5 residual U-blocks
    - upsample, join same-size encoder map
    note: Each stage upsamples the map from below, joins it with the encoder map of the same size and passes it through a residual U-block.
  - id: side
    role: projector
    title: Output convolution
    details:
    - 3 × 3 · to one channel
    note: A convolution turns the top decoder map into one channel, which is resized to the input size. The network also gives such maps from deeper stages; the card's example uses only the top one.
  - id: mask
    role: outputHead
    title: Foreground mask
    details:
    - sigmoid · one value per pixel
    note: A sigmoid gives values between 0 and 1. The card's example resizes the mask to the photo's size and uses it as the alpha channel to remove the background.
filters:
  size_category: tiny
  is_quantized: false
  is_gated: false
  pipeline_tag: image-segmentation
  library: transformers
hub_signals:
  downloads_30d: 229106
  likes: 2064
  last_modified: '2026-11-16'
  observed_at: '2026-11-21'
  source: https://huggingface.co/briaai/RMBG-1.4
aliases:
- briaai/RMBG-1.4
lineage:
  parents: []
  children: []
quotes:
  summary: BRIA RMBG 1.4 is a saliency segmentation model trained exclusively on a professional-grade dataset.
  intended_use: Developed by BRIA AI, RMBG v1.4 is available as a source-available model for non-commercial use.
  what_sets_it_apart: RMBG v1.4 is developed on the [IS-Net](https://github.com/xuebinqin/DIS) enhanced with our unique training scheme and proprietary dataset.
  running_it: model_input_size = [1024, 1024]
tags:
- SegformerForSemanticSegmentation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/briaai/RMBG-1.4
  title: briaai/RMBG-1.4 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/briaai/RMBG-1.4/blob/main/config.json
  title: briaai/RMBG-1.4 — config.json
  trust: 0.9
- url: https://huggingface.co/briaai/RMBG-1.4/blob/main/briarmbg.py
  title: briaai/RMBG-1.4 — briarmbg.py (model code)
  trust: 0.9
---


