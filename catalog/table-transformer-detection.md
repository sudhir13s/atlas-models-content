---
id: model/table-transformer-detection
type: model
slug: table-transformer-detection
schema_version: 1.1
title: table-transformer-detection
organization: organization/microsoft
release_date: '2022-10-14'
release_year: 2022
release_month: 10
license: license/mit
model_category: vision
tasks:
- task/object-detection
parameters_active_b: 0.03
total_parameters: 28818631
summary: Table Transformer (fine-tuned for table detection) is a model that finds the tables in an image of a document. The card says it is a Table Transformer (DETR) model trained on PubTables1M, introduced in the paper by Smock et al. and first released in the microsoft/table-transformer repository. The Hugging Face team wrote the card, because the releasing team did not.
intended_use: The card says the raw model can be used for detecting tables in documents and points to the Transformers documentation.
what_sets_it_apart: The card describes it as equivalent to DETR, a Transformer-based object detection model. It notes that the authors chose DETR's normalize-before setting, which applies layer normalization before the self- and cross-attention steps.
fingerprint:
  num_layers: 6
  hidden_size: 256
  activation_function: relu
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/table_transformer/modeling_table_transformer.py
  summary: A convolutional network turns the image into a grid of features and a transformer encoder (left) refines them. The decoder (right) starts from a fixed set of learned queries; each query reads the image features and ends as one predicted object.
  side_into: cross-attention
  stages:
  - id: image
    role: input
    title: Image
    side: true
  - id: backbone
    role: visionEncoder
    title: ResNet-18 backbone
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
    - 15 learned vectors
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
  - id: heads
    role: outputHead
    title: Class head + box head
    details:
    - a class and a box per query
    note: A linear layer gives each query a class (or no object), and a small network gives it a box.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: object-detection
  library: transformers
hub_signals:
  downloads_30d: 295500
  likes: 445
  last_modified: '2023-09-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/microsoft/table-transformer-detection
aliases:
- microsoft/table-transformer-detection
lineage:
  parents: []
  children: []
quotes:
  summary: Table Transformer (DETR) model trained on PubTables1M.
  intended_use: You can use the raw model for detecting tables in documents.
  what_sets_it_apart: the authors decided to use the "normalize before" setting of DETR
tags:
- object-detection
- table-transformer
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/microsoft/table-transformer-detection
  title: microsoft/table-transformer-detection — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/table-transformer-detection/blob/main/config.json
  title: microsoft/table-transformer-detection — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2110.00061
  title: arXiv:2110.00061
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/table_transformer/modeling_table_transformer.py
  title: 'transformers/models/table_transformer/modeling_table_transformer.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Table Transformer (fine-tuned for table detection) is a model that finds the tables in an image of a document. The card says it is a Table Transformer (DETR) model trained on PubTables1M, introduced in the paper by Smock et al. and first released in the microsoft/table-transformer repository. The Hugging Face team wrote the card, because the releasing team did not.

## Intended Use

The card says the raw model can be used for detecting tables in documents and points to the Transformers documentation.

## What Sets It Apart

The card describes it as equivalent to DETR, a Transformer-based object detection model. It notes that the authors chose DETR's normalize-before setting, which applies layer normalization before the self- and cross-attention steps.
