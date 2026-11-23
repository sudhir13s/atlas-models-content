---
id: task/depth-estimation
type: task
slug: depth-estimation
schema_version: 3.1
title: Depth Estimation
group: task-group/vision
order: 6
summary: Predict how far each pixel in an image is from the camera.
hf_pipeline_tags:
- depth-estimation
inputs:
- image
outputs:
- depth-map
tags:
- vision
- depth
- 3d
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/depth-estimation
  title: Hugging Face — Depth Estimation task
  trust: 0.9
---

## What It Is

A depth estimation model turns an ordinary image into a **depth map**: one distance value per pixel.

- **Single-image input:** most models work from one photo, with no second camera or depth sensor.
- **Relative depth** orders pixels from near to far but has no real-world unit.
- **Metric depth** gives distances in real units such as metres.

## When To Use

- **Use it** for background blur, 3D effects, scene reconstruction or obstacle awareness.
- **Use image segmentation instead** when you need object outlines rather than distance.
- **Use object detection instead** when you only need to know what is present and roughly where.
- **Use keypoint detection as well** when matching points across views to recover camera position.

## How To Choose

- **Relative or metric:** choose metric only when real distances matter; relative is often sharper.
- **Scene type:** models trained indoors can fail outdoors, and the reverse.
- **Detail at edges:** thin objects and boundaries are where models differ most.
- **Speed:** live video and robotics need a small model; offline processing can use a larger one.
