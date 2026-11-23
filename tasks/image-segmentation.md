---
id: task/image-segmentation
type: task
slug: image-segmentation
schema_version: 3.1
title: Image Segmentation
group: task-group/vision
order: 5
summary: Mark which pixels belong to each object or region in an image.
hf_pipeline_tags:
- image-segmentation
- mask-generation
inputs:
- image
outputs:
- mask
tags:
- vision
- segmentation
- masks
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/image-segmentation
  title: Hugging Face — Image Segmentation task
  trust: 0.9
---

## What It Is

An image segmentation model outputs **masks**: pixel-level outlines of the regions in an image.

- **Semantic segmentation** labels every pixel with a category but does not separate individual objects.
- **Instance segmentation** gives each object its own mask; **panoptic** combines both views.
- **Promptable mask generation** outlines whatever a click, box or other prompt points at, without a category label.

## When To Use

- **Use it** for background removal, measuring areas, or editing one region of a picture.
- **Use object detection instead** when a rough box is enough; it is faster and easier to label data for.
- **Use image classification instead** when you only need to know what the image contains.
- **Use depth estimation instead** when you need distance from the camera rather than object boundaries.

## How To Choose

- **Segmentation type:** semantic, instance, panoptic or promptable must match the output you need.
- **Label set:** category models cover fixed classes; promptable models cover any object but return no label.
- **Edge quality:** fine detail such as hair or thin structures varies widely between models.
- **Speed and memory:** high-resolution masks are costly, especially on video.
