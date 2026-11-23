---
id: model/dinov3-vitb16-pretrain-lvd1689m
type: model
slug: dinov3-vitb16-pretrain-lvd1689m
schema_version: 1.1
title: dinov3-vitb16-pretrain-lvd1689m
organization: organization/meta
release_date: '2025-08-05'
release_year: 2025
release_month: 8
license_note: dinov3-license
model_category: retrieval
tasks:
- task/image-and-multimodal-embeddings
total_parameters: 85660416
base_models:
- name: facebook/dinov3-vit7b16-pretrain-lvd1689m
  address: https://huggingface.co/facebook/dinov3-vit7b16-pretrain-lvd1689m
summary: 'DINOv3 ViT-B/16 is a vision backbone from Meta AI: it takes an image and returns numeric feature vectors that other tools can use for tasks such as classification, retrieval, depth estimation and segmentation. The model card lists it among the models distilled from the much larger DINOv3 ViT-7B and pretrained on web data (the LVD-1689M dataset).'
intended_use: 'The card presents the models as vision backbones that provide general-purpose features for downstream tasks. It lists uses without fine-tuning, with simple classifiers such as k-nearest neighbors or a linear layer: image classification, image retrieval, keypoint matching, depth estimation, semantic segmentation, object discovery, video segmentation tracking and video classification.'
what_sets_it_apart: DINOv3 is described as a family of 12 models, including a ViT-7B trained from scratch and smaller ViT and ConvNeXt models distilled from it. The card says each Transformer-based model takes an image and returns a class token, patch tokens and register tokens, with a patch size of 16 pixels, so a 224 by 224 image gives 201 tokens. Meta recommends keeping fine-tuning as a last resort, because the frozen features are expected to perform well as they are.
limitations: The card says that, compared with its predecessors, DINOv3 performs fairly evenly across income groups in geographic fairness tests, but with a notable drop in the low-income group, and a relative difference remains between Europe and Africa. It adds that fine-tuning is expected to increase biases in the features.
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: image-feature-extraction
  library: transformers
hub_signals:
  downloads_30d: 335592
  likes: 617
  last_modified: '2025-08-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/dinov3-vitb16-pretrain-lvd1689m
aliases:
- facebook/dinov3-vitb16-pretrain-lvd1689m
lineage:
  parents: []
  children: []
quotes:
  summary: The models are vision backbones providing multi-purpose features for downstream tasks.
  intended_use: 'The models can be used without fine-tuning, with downstream classifiers as simple as linear layers, to obtain competitive results:'
  what_sets_it_apart: 5 ViT-S/S+/B/L/H+ models distilled from the ViT-7B,
  limitations: Fine-tuning is expected to increase the biases in the features produced by the model as they will be tuned to the fine-tuning labels.
tags:
- image-feature-extraction
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/dinov3-vitb16-pretrain-lvd1689m
  title: facebook/dinov3-vitb16-pretrain-lvd1689m — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2508.10104
  title: arXiv:2508.10104
  trust: 0.95
---

## Summary

DINOv3 ViT-B/16 is a vision backbone from Meta AI: it takes an image and returns numeric feature vectors that other tools can use for tasks such as classification, retrieval, depth estimation and segmentation. The model card lists it among the models distilled from the much larger DINOv3 ViT-7B and pretrained on web data (the LVD-1689M dataset).

## Intended Use

The card presents the models as vision backbones that provide general-purpose features for downstream tasks. It lists uses without fine-tuning, with simple classifiers such as k-nearest neighbors or a linear layer: image classification, image retrieval, keypoint matching, depth estimation, semantic segmentation, object discovery, video segmentation tracking and video classification.

## What Sets It Apart

DINOv3 is described as a family of 12 models, including a ViT-7B trained from scratch and smaller ViT and ConvNeXt models distilled from it. The card says each Transformer-based model takes an image and returns a class token, patch tokens and register tokens, with a patch size of 16 pixels, so a 224 by 224 image gives 201 tokens. Meta recommends keeping fine-tuning as a last resort, because the frozen features are expected to perform well as they are.

## Limitations

The card says that, compared with its predecessors, DINOv3 performs fairly evenly across income groups in geographic fairness tests, but with a notable drop in the low-income group, and a relative difference remains between Europe and Africa. It adds that fine-tuning is expected to increase biases in the features.
