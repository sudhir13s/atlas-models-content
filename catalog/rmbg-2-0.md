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
---

## Summary

RMBG-2.0 is a background removal model from BRIA AI. Given a photo, it separates the foreground from the background. The card calls it a dichotomous image segmentation model, which cuts an image into a main object and everything else, and says it outputs a single-channel 8-bit grayscale alpha matte, a map of how opaque each pixel is.

## Intended Use

The card says the model is designed to separate foreground from background across a range of categories and image types, and that it was trained on stock images and on e-commerce, gaming and advertising content. The weights are released under CC BY-NC 4.0 for non-commercial use, and the card says commercial use needs an agreement with BRIA.

## What Sets It Apart

Because the output is a grayscale matte rather than a hard black-and-white mask, the card says developers can set their own thresholds for foreground and background to suit different uses. The card also says the model was trained exclusively on a professional-grade dataset made up of legally licensed data.
