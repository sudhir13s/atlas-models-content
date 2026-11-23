---
id: model/rmbg-2-0
type: model
slug: rmbg-2-0
schema_version: 1.1
title: RMBG-2.0
organization: organization/briaai
release_date: '2024-10-29'
release_year: 2024
release_month: 10
license_note: bria-rmbg-2.0
model_category: vision
tasks:
- task/image-segmentation
total_parameters: 220700242
summary: RMBG-2.0 is a background removal model from BRIA AI. Given a photo, it separates the foreground from the background. The card calls it a dichotomous image segmentation model, which cuts an image into a main object and everything else, and says it outputs a single-channel 8-bit grayscale alpha matte, a map of how opaque each pixel is.
intended_use: The card says the model is designed to separate foreground from background across a range of categories and image types, and that it was trained on stock images and on e-commerce, gaming and advertising content. The weights are released under CC BY-NC 4.0 for non-commercial use, and the card says commercial use needs an agreement with BRIA.
what_sets_it_apart: Because the output is a grayscale matte rather than a hard black-and-white mask, the card says developers can set their own thresholds for foreground and background to suit different uses. The card also says the model was trained exclusively on a professional-grade dataset made up of legally licensed data.
pipeline:
  source: https://arxiv.org/abs/2401.03407
  summary: The card says RMBG-2.0 is built on the BiRefNet (bilateral reference network) architecture; the stages follow the BiRefNet paper. A transformer encoder finds the object in the photo, and a decoder rebuilds a sharp mask step by step, each step also looking at full-resolution patches of the photo (left).
  side_into: decoder
  stages:
  - id: patches
    role: input
    title: Full-resolution patches
    details:
    - cropped to each decoder size
    note: The photo at its original resolution is cut into patches whose size matches the decoder stage, and these are stacked with that stage's features (the paper's inward reference).
    side: true
  - id: image
    role: input
    title: Image
    details:
    - resized to 1024 × 1024
  - id: encoder
    role: visionEncoder
    title: Transformer encoder
    details:
    - four stages · 1/4 to 1/32 size
    note: A hierarchical vision transformer gives feature maps at four sizes. The first three also go to the decoder stage of the same size through 1 × 1 convolutions. The card does not say which encoder size RMBG-2.0 uses.
  - id: localization
    role: projector
    title: Localization module
    details:
    - ASPP · squeezes the features
    note: The encoder's maps are stacked and squeezed, and atrous spatial pyramid pooling (ASPP) mixes context from several receptive-field sizes to locate the object.
  - id: decoder
    role: feedForward
    title: Reconstruction block
    details:
    - deformable convs · 1×1, 3×3, 7×7
    note: Deformable convolutions with three receptive-field sizes rebuild the features. A predicted gradient map turns into an attention map that stresses areas with fine detail (the paper's outward reference).
    frame: BiRef block
  - id: upsample
    role: normalisation
    title: Add encoder map, upsample
    note: The block's output is added to the encoder map of the same size and upsampled for the next block, so the mask is rebuilt from coarse to fine.
    frame: BiRef block
  - id: mask
    role: outputHead
    title: Alpha matte
    details:
    - sigmoid · one value per pixel
    note: The card's example takes the last prediction, applies a sigmoid, resizes it to the photo and uses it as the alpha channel.
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: image-segmentation
  library: transformers
hub_signals:
  downloads_30d: 391793
  likes: 1660
  last_modified: '2026-11-16'
  observed_at: '2026-11-21'
  source: https://huggingface.co/briaai/RMBG-2.0
aliases:
- briaai/RMBG-2.0
lineage:
  parents: []
  children: []
quotes:
  summary: BRIA RMBG-2.0 is a dichotomous image segmentation model trained exclusively on a professional-grade dataset.
  intended_use: The model is designed to effectively separate foreground from background in a range of categories and image types.
  what_sets_it_apart: The model output includes a single-channel 8-bit grayscale alpha matte, where each pixel value indicates the opacity level of the corresponding pixel in the original image.
tags:
- image-segmentation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/briaai/RMBG-2.0
  title: briaai/RMBG-2.0 — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2401.03407
  title: 'arXiv:2401.03407: Bilateral Reference for High-Resolution Dichotomous Image Segmentation (BiRefNet)'
  trust: 0.95
---

## Summary

RMBG-2.0 is a background removal model from BRIA AI. Given a photo, it separates the foreground from the background. The card calls it a dichotomous image segmentation model, which cuts an image into a main object and everything else, and says it outputs a single-channel 8-bit grayscale alpha matte, a map of how opaque each pixel is.

## Intended Use

The card says the model is designed to separate foreground from background across a range of categories and image types, and that it was trained on stock images and on e-commerce, gaming and advertising content. The weights are released under CC BY-NC 4.0 for non-commercial use, and the card says commercial use needs an agreement with BRIA.

## What Sets It Apart

Because the output is a grayscale matte rather than a hard black-and-white mask, the card says developers can set their own thresholds for foreground and background to suit different uses. The card also says the model was trained exclusively on a professional-grade dataset made up of legally licensed data.
