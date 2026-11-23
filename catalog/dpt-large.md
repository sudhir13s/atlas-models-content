---
id: model/dpt-large
type: model
slug: dpt-large
schema_version: 1.1
title: dpt-large
organization: organization/intel
license: license/apache-2-0
model_category: vision
tasks:
- task/depth-estimation
parameters_active_b: 0.34
total_parameters: 341850305
summary: DPT-Large, also known as MiDaS 3.0, is a depth estimation model from Intel. From a single ordinary photo it predicts how far away each part of the scene is, a task called monocular depth estimation. The card says it is a Dense Prediction Transformer trained on 1.4 million images.
intended_use: The card lists zero-shot monocular depth estimation as the primary use, for anyone doing that task. It says that in most cases the model will need to be fine-tuned for a particular task, and that it should not be used to intentionally create hostile or alienating environments for people.
what_sets_it_apart: The card says DPT uses a Vision Transformer (ViT) as its backbone and adds a neck and a head on top to produce the depth map.
fingerprint:
  attention_type: MHA
  num_layers: 24
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  intermediate_size: 4096
  activation_function: gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: depth-estimation
  library: transformers
hub_signals:
  downloads_30d: 27715
  likes: 208
  last_modified: '2024-02-24'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Intel/dpt-large
aliases:
- Intel/dpt-large
lineage:
  parents: []
  children: []
quotes:
  summary: Dense Prediction Transformer (DPT) model trained on 1.4 million images for monocular depth estimation.
  intended_use: This model in most cases will need to be fine-tuned for your particular task.
  what_sets_it_apart: DPT uses the Vision Transformer (ViT) as backbone and adds a neck + head on top for monocular depth estimation.
tags:
- depth-estimation
- dpt
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Intel/dpt-large
  title: Intel/dpt-large — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Intel/dpt-large/blob/main/config.json
  title: Intel/dpt-large — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2103.13413
  title: arXiv:2103.13413
  trust: 0.95
---

## Summary

DPT-Large, also known as MiDaS 3.0, is a depth estimation model from Intel. From a single ordinary photo it predicts how far away each part of the scene is, a task called monocular depth estimation. The card says it is a Dense Prediction Transformer trained on 1.4 million images.

## Intended Use

The card lists zero-shot monocular depth estimation as the primary use, for anyone doing that task. It says that in most cases the model will need to be fine-tuned for a particular task, and that it should not be used to intentionally create hostile or alienating environments for people.

## What Sets It Apart

The card says DPT uses a Vision Transformer (ViT) as its backbone and adds a neck and a head on top to produce the depth map.
