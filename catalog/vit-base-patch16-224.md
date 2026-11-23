---
id: model/vit-base-patch16-224
type: model
slug: vit-base-patch16-224
schema_version: 1.1
title: vit-base-patch16-224
organization: organization/google-deepmind
license: license/apache-2-0
architecture_id: architecture-era/vision-transformer
model_category: vision
tasks:
- task/image-classification
parameters_active_b: 0.09
total_parameters: 86567656
summary: This is the base-sized Vision Transformer (ViT), an image classification model introduced in the paper "An Image is Worth 16x16 Words" by Dosovitskiy et al. It was pre-trained on ImageNet-21k (14 million images, 21,843 classes) and fine-tuned on ImageNet 2012 (1 million images, 1,000 classes), both at 224 by 224 pixels. The weights were converted from the timm repository, which had itself converted them from JAX to PyTorch, and the Hugging Face team wrote the card.
intended_use: The card says the raw model can be used for image classification, and points to the model hub for versions fine-tuned on other tasks. Its example classifies a photo into one of the 1,000 ImageNet classes.
what_sets_it_apart: The card describes a BERT-like transformer encoder that reads an image as a sequence of fixed-size 16 by 16 patches, each linearly embedded. A [CLS] token is added at the start of the sequence to be used for classification, along with absolute position embeddings.
running_it: The card says images are resized to 224 by 224 and normalized with a mean and standard deviation of 0.5 for each color channel. It notes that for fine-tuning, the best results come from a higher resolution of 384 by 384.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: pre
  stack: encoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: image-classification
  library: transformers
hub_signals:
  downloads_30d: 15301431
  likes: 1058
  last_modified: '2023-09-05'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/vit-base-patch16-224
aliases:
- google/vit-base-patch16-224
lineage:
  parents: []
  children: []
quotes:
  summary: Vision Transformer (ViT) model pre-trained on ImageNet-21k (14 million images, 21,843 classes) at resolution 224x224, and fine-tuned on ImageNet 2012 (1 million images, 1,000 classes) at resolution 224x224.
  intended_use: You can use the raw model for image classification.
  what_sets_it_apart: Images are presented to the model as a sequence of fixed-size patches (resolution 16x16), which are linearly embedded.
  running_it: Images are resized/rescaled to the same resolution (224x224) and normalized across the RGB channels with mean (0.5, 0.5, 0.5) and standard deviation (0.5, 0.5, 0.5).
tags:
- image-classification
- vit
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/vit-base-patch16-224
  title: google/vit-base-patch16-224 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/vit-base-patch16-224/blob/main/config.json
  title: google/vit-base-patch16-224 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2010.11929
  title: arXiv:2010.11929
  trust: 0.95
- url: https://arxiv.org/abs/2006.03677
  title: arXiv:2006.03677
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vit/modeling_vit.py
  title: 'transformers/models/vit/modeling_vit.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

This is the base-sized Vision Transformer (ViT), an image classification model introduced in the paper "An Image is Worth 16x16 Words" by Dosovitskiy et al. It was pre-trained on ImageNet-21k (14 million images, 21,843 classes) and fine-tuned on ImageNet 2012 (1 million images, 1,000 classes), both at 224 by 224 pixels. The weights were converted from the timm repository, which had itself converted them from JAX to PyTorch, and the Hugging Face team wrote the card.

## Intended Use

The card says the raw model can be used for image classification, and points to the model hub for versions fine-tuned on other tasks. Its example classifies a photo into one of the 1,000 ImageNet classes.

## What Sets It Apart

The card describes a BERT-like transformer encoder that reads an image as a sequence of fixed-size 16 by 16 patches, each linearly embedded. A [CLS] token is added at the start of the sequence to be used for classification, along with absolute position embeddings.

## Running It

The card says images are resized to 224 by 224 and normalized with a mean and standard deviation of 0.5 for each color channel. It notes that for fine-tuning, the best results come from a higher resolution of 384 by 384.
