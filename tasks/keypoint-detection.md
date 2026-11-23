---
id: task/keypoint-detection
type: task
slug: keypoint-detection
schema_version: 3.1
title: Keypoint Detection
group: task-group/vision
order: 4
summary: Locate specific points in an image, such as body joints or distinctive features used to match two views.
hf_pipeline_tags:
- keypoint-detection
inputs:
- image
outputs:
- keypoints
tags:
- vision
- pose-estimation
- feature-matching
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/keypoint-detection
  title: Hugging Face — Keypoint Detection task
  trust: 0.9
---

## What It Is

A keypoint detection model returns the **coordinates of meaningful points** in an image.

- **Pose estimation** finds a fixed set of joints on a person, hand, face or animal.
- **Feature points** are distinctive corners and textures with a descriptor, used to match the same spot across images.
- **Output per point:** a position and usually a confidence score.

## When To Use

- **Use it** for motion analysis, gesture input, or aligning and stitching overlapping photos.
- **Use object detection instead** when a box around each person or object is enough.
- **Use image segmentation instead** when you need the full outline rather than a set of points.
- **Use depth estimation as well** when points must be placed in three dimensions.

## How To Choose

- **Point set:** which joints or landmarks the model predicts, and for which subject.
- **Single or multiple subjects:** some models need a cropped person from a detector first.
- **Speed:** live tracking needs a model that keeps up with the camera frame rate.
- **Robustness:** check behaviour when parts are hidden, blurred or in unusual poses.
