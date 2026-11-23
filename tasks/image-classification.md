---
id: task/image-classification
type: task
slug: image-classification
schema_version: 3.1
title: Image Classification
group: task-group/vision
order: 2
summary: Assign one or more labels to a whole image, from a fixed list or from labels supplied with the request.
hf_pipeline_tags:
- image-classification
- zero-shot-image-classification
inputs:
- image
outputs:
- label
tags:
- vision
- classification
- zero-shot
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/image-classification
  title: Hugging Face — Image Classification task
  trust: 0.9
---

## What It Is

An image classification model looks at a whole image and **says which category it belongs to**.

- **Fixed-label models** choose from the categories they were trained on and return a score for each.
- **Zero-shot models** accept the candidate labels as text at request time, with no retraining.
- **One answer per image:** the model does not say where in the picture the subject is.

## When To Use

- **Use it** for sorting, filtering or tagging images where one label per image is enough.
- **Use object detection instead** when you need to locate or count items in the image.
- **Use image segmentation instead** when you need the exact outline of each region.
- **Use a vision-language model instead** when the answer is a description rather than a label.

## How To Choose

- **Label set:** a fixed-label model only works if its categories match yours; otherwise fine-tune or go zero-shot.
- **Speed and size:** small models run on phones and cameras; larger ones are more accurate.
- **Input resolution:** fine-grained categories need more pixels than broad ones.
- **Training domain:** models trained on everyday photos transfer poorly to medical, satellite or industrial images.
