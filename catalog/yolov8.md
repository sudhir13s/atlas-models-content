---
id: model/yolov8
type: model
slug: yolov8
schema_version: 1.1
title: YOLOv8
organization: organization/ultralytics
release_date: '2024-01-31'
release_year: 2024
release_month: 1
license_note: agpl-3.0
model_category: vision
tasks:
- task/object-detection
summary: YOLOv8 is a family of computer-vision models from Ultralytics for tasks such as object detection, which means locating objects in an image and drawing a box around each one. The card lists object detection, tracking, instance segmentation, semantic segmentation, image classification and pose estimation as the tasks these models handle. The card's tags also mention other YOLO versions, so the page is a general Ultralytics overview.
running_it: The card says to install the ultralytics package with pip, in an environment with Python 3.8 or later and PyTorch 1.8 or later, and shows the yolo command line, for example yolo predict with a YOLOv8n model. The page lists the AGPL-3.0 licence, and the card says commercial use needs an Enterprise License from Ultralytics.
pipeline:
  source: https://github.com/ultralytics/ultralytics/blob/main/ultralytics/cfg/models/v8/yolov8.yaml
  summary: 'The YOLOv8 object detector as its model file defines it; the sizes n, s, m, l and x share this layout and differ in depth and width. A convolutional backbone shrinks the image to 1/8, 1/16 and 1/32 of its size, a neck mixes these three maps top-down and bottom-up, and a head predicts boxes and classes at every position of all three.'
  stages:
  - id: image
    role: input
    title: Image
    details:
    - 640 × 640 pixels
  - id: stem
    role: embedding
    title: Strided convolution
    details:
    - to 1/2 of the image size
  - id: backbone
    role: visionEncoder
    title: Strided conv + C2f block
    details:
    - to 1/4, 1/8, 1/16, 1/32 of the size
    note: Each stage halves the map with a stride-2 convolution, then a C2f block (convolutions with split and joined branches) refines it. The outputs at 1/8, 1/16 and 1/32 are kept for the neck.
    frame: Backbone stage
    repeat: 4
  - id: sppf
    role: projector
    title: SPPF
    details:
    - pooling at several sizes
    note: Spatial pyramid pooling, fast (SPPF) applies max-pooling in sequence and joins the results, mixing context from larger areas.
  - id: neck-down
    role: feedForward
    title: Upsample + join + C2f
    details:
    - top-down · twice
    note: The deepest map is upsampled, joined with the backbone map of the same size and refined, down to the 1/8 map.
  - id: neck-up
    role: feedForward
    title: Strided conv + join + C2f
    details:
    - bottom-up · twice
    note: The 1/8 map is shrunk again and joined with the neck maps of the same size, giving refined maps at 1/8, 1/16 and 1/32.
  - id: head
    role: outputHead
    title: Detect head
    details:
    - box branch + class branch
    - at all three scales · no anchors
    note: At every position of each map, one convolution branch predicts box edges as distributions (Distribution Focal Loss) and another predicts scores for the 80 classes.
  - id: boxes
    role: outputHead
    title: Boxes + classes
    details:
    - after non-maximum suppression
filters:
  is_gated: false
  pipeline_tag: object-detection
  library: ultralytics
hub_signals:
  downloads_30d: 58739
  likes: 403
  last_modified: '2026-06-26'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Ultralytics/YOLOv8
aliases:
- Ultralytics/YOLOv8
lineage:
  parents: []
  children: []
quotes:
  summary: They excel at [object detection](https://docs.ultralytics.com/tasks/detect)
  running_it: Request an Enterprise License for commercial use at
tags:
- object-detection
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Ultralytics/YOLOv8
  title: Ultralytics/YOLOv8 — model page (Hugging Face)
  trust: 0.9
- url: https://github.com/ultralytics/ultralytics/blob/main/ultralytics/cfg/models/v8/yolov8.yaml
  title: 'ultralytics/cfg/models/v8/yolov8.yaml: YOLOv8 detection model definition (Ultralytics)'
  trust: 0.9
- url: https://github.com/ultralytics/ultralytics/blob/main/ultralytics/nn/modules/head.py
  title: 'ultralytics/nn/modules/head.py: Detect head code (Ultralytics)'
  trust: 0.9
---

## Summary

YOLOv8 is a family of computer-vision models from Ultralytics for tasks such as object detection, which means locating objects in an image and drawing a box around each one. The card lists object detection, tracking, instance segmentation, semantic segmentation, image classification and pose estimation as the tasks these models handle. The card's tags also mention other YOLO versions, so the page is a general Ultralytics overview.

## Running It

The card says to install the ultralytics package with pip, in an environment with Python 3.8 or later and PyTorch 1.8 or later, and shows the yolo command line, for example yolo predict with a YOLOv8n model. The page lists the AGPL-3.0 licence, and the card says commercial use needs an Enterprise License from Ultralytics.
