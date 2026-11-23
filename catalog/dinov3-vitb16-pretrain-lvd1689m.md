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
pipeline:
  source: https://github.com/facebookresearch/dinov3/blob/main/dinov3/hub/backbones.py
  summary: The image is cut into 16 × 16 pixel patches, each turned into a vector, and one class token and four register tokens are placed in front. A stack of 12 transformer blocks refines all the tokens. The output is one vector for the whole image (the class token) and one vector per patch.
  stages:
  - id: image
    role: input
    title: Image
    details:
    - sides a multiple of 16 pixels
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 16 × 16 pixel patches · width 768
    note: A convolution turns each 16 by 16 pixel patch into one vector of width 768.
  - id: tokens
    role: embedding
    title: Class + 4 register tokens
    details:
    - placed before the patches
    note: One learned class token and four learned register tokens are placed in front of the patch vectors. No position vectors are added here; positions enter inside attention as rotary embeddings.
  - id: block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 12 heads · RoPE positions
    - MLP feed-forward · LayerScale
    note: Each block applies a LayerNorm, self-attention with rotary position embedding (RoPE) and a learned per-channel scale, then a LayerNorm, a two-layer MLP and another scale; each part is added back to its input.
    frame: Encoder block
    repeat: 12
  - id: norm
    role: normalisation
    title: Final LayerNorm
  - id: output
    role: outputHead
    title: Class token + patch tokens
    details:
    - one vector per image and per patch
    note: The class token after the final norm is the image embedding; the patch tokens give one feature per 16 by 16 patch. A 224 × 224 image gives 1 class, 4 register and 196 patch tokens.
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
- url: https://github.com/facebookresearch/dinov3/blob/main/dinov3/hub/backbones.py
  title: 'dinov3/hub/backbones.py: model definitions (Meta, DINOv3 repository)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/dinov3_vit/modeling_dinov3_vit.py
  title: 'transformers/models/dinov3_vit/modeling_dinov3_vit.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

DINOv3 ViT-B/16 is a vision backbone from Meta AI: it takes an image and returns numeric feature vectors that other tools can use for tasks such as classification, retrieval, depth estimation and segmentation. The model card lists it among the models distilled from the much larger DINOv3 ViT-7B and pretrained on web data (the LVD-1689M dataset).

## Intended Use

The card presents the models as vision backbones that provide general-purpose features for downstream tasks. It lists uses without fine-tuning, with simple classifiers such as k-nearest neighbors or a linear layer: image classification, image retrieval, keypoint matching, depth estimation, semantic segmentation, object discovery, video segmentation tracking and video classification.

## What Sets It Apart

DINOv3 is described as a family of 12 models, including a ViT-7B trained from scratch and smaller ViT and ConvNeXt models distilled from it. The card says each Transformer-based model takes an image and returns a class token, patch tokens and register tokens, with a patch size of 16 pixels, so a 224 by 224 image gives 201 tokens. Meta recommends keeping fine-tuning as a last resort, because the frozen features are expected to perform well as they are.

## Limitations

The card says that, compared with its predecessors, DINOv3 performs fairly evenly across income groups in geographic fairness tests, but with a notable drop in the low-income group, and a relative difference remains between Europe and Africa. It adds that fine-tuning is expected to increase biases in the features.
