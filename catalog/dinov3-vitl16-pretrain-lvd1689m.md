---
id: model/dinov3-vitl16-pretrain-lvd1689m
type: model
slug: dinov3-vitl16-pretrain-lvd1689m
schema_version: 1.1
title: dinov3-vitl16-pretrain-lvd1689m
organization: organization/meta
release_date: '2025-08-06'
release_year: 2025
release_month: 8
license_note: dinov3-license
model_category: retrieval
tasks:
- task/image-and-multimodal-embeddings
total_parameters: 303129600
base_models:
- name: facebook/dinov3-vit7b16-pretrain-lvd1689m
  address: https://huggingface.co/facebook/dinov3-vit7b16-pretrain-lvd1689m
summary: 'DINOv3 ViT-L/16 is a vision model from Meta AI that reads an image and returns numeric features describing it, rather than a label or a caption. The card calls the DINOv3 models vision backbones: other programs build tasks such as classification or depth estimation on top of their features. This checkpoint is a Vision Transformer with a patch size of 16, pretrained on web data from the LVD-1689M dataset and distilled from the family''s ViT-7B model. Access on Hugging Face requires agreeing to share Hugging Face account information with Meta.'
intended_use: The card says the models provide multi-purpose features for downstream tasks and can be used without fine-tuning, with downstream classifiers as simple as a linear layer. Its list covers image classification, image retrieval with nearest neighbors, 3D keypoint correspondences, depth estimation and semantic segmentation, unsupervised object discovery, video segmentation tracking and video classification.
what_sets_it_apart: The card describes DINOv3 as a family of vision foundation models that produces dense features, meaning one feature per image patch as well as one for the whole image. For a 224x224 image this checkpoint returns 1 class token, 4 register tokens and 196 patch tokens, and it accepts larger images whose sides are multiples of 16. The card says the smaller models in the family, this one included, were distilled from a 7-billion-parameter ViT trained from scratch.
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: image-feature-extraction
  library: transformers
hub_signals:
  downloads_30d: 702105
  likes: 2714
  last_modified: '2025-08-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/dinov3-vitl16-pretrain-lvd1689m
aliases:
- facebook/dinov3-vitl16-pretrain-lvd1689m
lineage:
  parents: []
  children: []
quotes:
  summary: The models are vision backbones providing multi-purpose features for downstream tasks.
  intended_use: The models can be used without fine-tuning, with downstream classifiers as simple as linear layers
  what_sets_it_apart: DINOv3 produces high-quality dense features
tags:
- image-feature-extraction
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/dinov3-vitl16-pretrain-lvd1689m
  title: facebook/dinov3-vitl16-pretrain-lvd1689m — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2508.10104
  title: arXiv:2508.10104
  trust: 0.95
---

## Summary

DINOv3 ViT-L/16 is a vision model from Meta AI that reads an image and returns numeric features describing it, rather than a label or a caption. The card calls the DINOv3 models vision backbones: other programs build tasks such as classification or depth estimation on top of their features. This checkpoint is a Vision Transformer with a patch size of 16, pretrained on web data from the LVD-1689M dataset and distilled from the family's ViT-7B model. Access on Hugging Face requires agreeing to share Hugging Face account information with Meta.

## Intended Use

The card says the models provide multi-purpose features for downstream tasks and can be used without fine-tuning, with downstream classifiers as simple as a linear layer. Its list covers image classification, image retrieval with nearest neighbors, 3D keypoint correspondences, depth estimation and semantic segmentation, unsupervised object discovery, video segmentation tracking and video classification.

## What Sets It Apart

The card describes DINOv3 as a family of vision foundation models that produces dense features, meaning one feature per image patch as well as one for the whole image. For a 224x224 image this checkpoint returns 1 class token, 4 register tokens and 196 patch tokens, and it accepts larger images whose sides are multiples of 16. The card says the smaller models in the family, this one included, were distilled from a 7-billion-parameter ViT trained from scratch.
