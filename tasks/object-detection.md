---
id: task/object-detection
type: task
slug: object-detection
schema_version: 3.1
title: Object Detection
group: task-group/vision
order: 3
summary: Find each object in an image and return a labelled box around it.
hf_pipeline_tags:
- object-detection
- zero-shot-object-detection
inputs:
- image
outputs:
- bounding-box
- label
tags:
- vision
- detection
- open-vocabulary
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/object-detection
  title: Hugging Face — Object Detection task
  trust: 0.9
---

## What It Is

An object detection model **locates and names** the things in an image.

- **Output per object:** a rectangular bounding box, a label and a confidence score.
- **Closed-set detectors** find only the categories they were trained on.
- **Open-vocabulary detectors** take a text prompt naming what to find, so new categories need no retraining.

## When To Use

- **Use it** to count items, track where they are, or crop them out for a later step.
- **Use image classification instead** when one label for the whole image is enough.
- **Use image segmentation instead** when a box is too coarse and you need the exact shape.
- **Use keypoint detection instead** when you need the position of body joints or specific points, not whole objects.

## How To Choose

- **Category coverage:** closed-set for a stable list, open-vocabulary when the list changes often.
- **Speed:** real-time video needs a small, fast detector; offline batches can afford a larger one.
- **Small-object accuracy:** depends heavily on input resolution and model design.
- **Licence:** several widely used detectors carry terms that restrict commercial use.
