---
id: model/detr-resnet-50
type: model
slug: detr-resnet-50
schema_version: 3.0
title: detr-resnet-50
organization: organization/meta
license: license/apache-2-0
model_category: vision
model_kind: transformer-vision
tasks:
- task/object-detection
parameters_active_b: 0.04
total_parameters: 41631008
summary: 'DETR (DEtection TRansformer) with a ResNet-50 backbone is an object detection model: given an image, it outputs a box and a class label for each object it finds. It was introduced in the paper by Carion et al. and first released in the facebookresearch repository, and the card says it was trained on COCO 2017, a set of 118 thousand annotated images. The Hugging Face team wrote the card, because the releasing team did not.'
intended_use: The card says the raw model can be used for object detection and points to the model hub for other DETR models.
what_sets_it_apart: 'The card describes an encoder-decoder transformer on top of a convolutional backbone, which uses 100 object queries, each of which looks for one particular object in the image. Training uses a bipartite matching loss: the Hungarian algorithm pairs each query with one ground-truth annotation, so that every object is matched exactly once.'
fingerprint:
  num_layers: 6
  hidden_size: 256
  activation_function: relu
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/detr/modeling_detr.py
  summary: A convolutional network turns the image into a grid of features and a transformer encoder (left) refines them. The decoder starts from a fixed set of learned queries; each query reads the image features and ends as one predicted object, with a class from one head and a box from a second head (right).
  side_into: cross-attention
  branch_from: norm
  stages:
  - id: image
    role: input
    title: Image
    side: true
  - id: backbone
    role: visionEncoder
    title: ResNet-50 backbone
    details:
    - convolutional network
    side: true
  - id: projection
    role: projector
    title: 1 × 1 convolution
    details:
    - to width 256
    side: true
  - id: encoder-block
    role: attention
    title: Attention + feed-forward
    details:
    - 8 heads · sine positions each layer
    frame: Encoder block
    repeat: 6
    side: true
  - id: queries
    role: input
    title: Object queries
    details:
    - 100 learned vectors
    note: A fixed set of learned vectors. Each one can become one detected object, so the model predicts at most this many objects per image.
  - id: self-attention
    role: attention
    title: Self-attention
    details:
    - 8 heads · between the queries
    frame: Decoder block
    repeat: 6
  - id: cross-attention
    role: attention
    title: Cross-attention
    details:
    - reads the image features
    frame: Decoder block
  - id: feed-forward
    role: feedForward
    title: Feed-forward
    details:
    - ReLU
    frame: Decoder block
  - id: norm
    role: normalisation
    title: Final LayerNorm
  - id: class-head
    role: outputHead
    title: Class head
    details:
    - a class per query
    note: A linear layer gives each query a class, or no object.
  - id: box-head
    role: outputHead
    title: Box head
    details:
    - a box per query
    note: A small network gives each query a box, read from the same decoder output as the class.
    branch: true
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: object-detection
  library: transformers
hub_signals:
  downloads_30d: 113423
  likes: 991
  last_modified: '2024-04-10'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/detr-resnet-50
aliases:
- facebook/detr-resnet-50
lineage:
  parents: []
  children: []
quotes:
  summary: DEtection TRansformer (DETR) model trained end-to-end on COCO 2017 object detection (118k annotated images).
  intended_use: You can use the raw model for object detection.
  what_sets_it_apart: Each object query looks for a particular object in the image.
tags:
- detr
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/detr-resnet-50
  title: facebook/detr-resnet-50 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/detr-resnet-50/blob/main/config.json
  title: facebook/detr-resnet-50 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2005.12872
  title: arXiv:2005.12872
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/detr/modeling_detr.py
  title: 'transformers/models/detr/modeling_detr.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


