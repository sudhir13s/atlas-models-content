---
id: model/dinov3-vits16-pretrain-lvd1689m
type: model
slug: dinov3-vits16-pretrain-lvd1689m
schema_version: 1.1
title: dinov3-vits16-pretrain-lvd1689m
organization: organization/meta
release_date: '2025-08-05'
release_year: 2025
release_month: 8
license_note: dinov3-license
model_category: retrieval
tasks:
- task/image-and-multimodal-embeddings
total_parameters: 21596544
base_models:
- name: facebook/dinov3-vit7b16-pretrain-lvd1689m
  address: https://huggingface.co/facebook/dinov3-vit7b16-pretrain-lvd1689m
summary: 'DINOv3 ViT-S/16 is a small vision backbone from Meta AI: it takes an image and returns numeric features, called tokens, that other models use for tasks such as classification or segmentation. The card says it was pretrained on the LVD-1689M web image dataset by distillation from the ViT-7B model of the same family, and that it uses a patch size of 16 pixels. It is a general feature extractor rather than a model that gives a final answer by itself.'
intended_use: The card calls the models vision backbones providing multi-purpose features for downstream tasks. It says they can be used without fine-tuning, with downstream classifiers as simple as linear layers, for image classification, image retrieval, depth estimation, semantic segmentation, video classification and other tasks, and that fine-tuning should be a last resort.
what_sets_it_apart: The card lists this model among five ViT models (S, S+, B, L, H+) distilled from the DINOv3 ViT-7B, which was trained from scratch. Each Transformer-based model returns a class token plus patch tokens and register tokens, and for a 224x224 image this gives 1 class token, 4 register tokens and 196 patch tokens.
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
    - 16 × 16 pixel patches · width 384
    note: A convolution turns each 16 by 16 pixel patch into one vector of width 384.
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
    - 6 heads · RoPE positions
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
  downloads_30d: 167624
  likes: 527
  last_modified: '2025-08-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/dinov3-vits16-pretrain-lvd1689m
aliases:
- facebook/dinov3-vits16-pretrain-lvd1689m
lineage:
  parents: []
  children: []
quotes:
  summary: The models are vision backbones providing multi-purpose features for downstream tasks.
  intended_use: While fine-tuning the models can yield some gains, it is recommended to keep this option as a last resort
  what_sets_it_apart: 5 ViT-S/S+/B/L/H+ models distilled from the ViT-7B
tags:
- image-feature-extraction
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/dinov3-vits16-pretrain-lvd1689m
  title: facebook/dinov3-vits16-pretrain-lvd1689m — model page (Hugging Face)
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

DINOv3 ViT-S/16 is a small vision backbone from Meta AI: it takes an image and returns numeric features, called tokens, that other models use for tasks such as classification or segmentation. The card says it was pretrained on the LVD-1689M web image dataset by distillation from the ViT-7B model of the same family, and that it uses a patch size of 16 pixels. It is a general feature extractor rather than a model that gives a final answer by itself.

## Intended Use

The card calls the models vision backbones providing multi-purpose features for downstream tasks. It says they can be used without fine-tuning, with downstream classifiers as simple as linear layers, for image classification, image retrieval, depth estimation, semantic segmentation, video classification and other tasks, and that fine-tuning should be a last resort.

## What Sets It Apart

The card lists this model among five ViT models (S, S+, B, L, H+) distilled from the DINOv3 ViT-7B, which was trained from scratch. Each Transformer-based model returns a class token plus patch tokens and register tokens, and for a 224x224 image this gives 1 class token, 4 register tokens and 196 patch tokens.
