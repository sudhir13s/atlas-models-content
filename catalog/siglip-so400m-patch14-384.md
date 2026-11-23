---
id: model/siglip-so400m-patch14-384
type: model
slug: siglip-so400m-patch14-384
schema_version: 1.1
title: siglip-so400m-patch14-384
organization: organization/google-deepmind
release_date: '2024-01-08'
release_year: 2024
release_month: 1
license: license/apache-2-0
model_category: vision
tasks:
- task/image-classification
parameters_active_b: 0.88
total_parameters: 877960498
summary: SigLIP is a model that matches images with text, so an image and a caption can be compared by how well they fit. This checkpoint is the shape-optimized SoViT-400m version, pre-trained on the WebLI dataset at 384 by 384 pixel resolution, from the paper "Sigmoid Loss for Language Image Pre-Training" and the Google big_vision repository. The team behind it wrote no model card, so the Hugging Face team wrote this one.
intended_use: The card says the raw model can be used for zero-shot image classification, where an image is scored against label texts supplied at run time without extra training, and for image-text retrieval. It points to the model hub for versions fine-tuned for other tasks.
what_sets_it_apart: The card describes SigLIP as CLIP, a multimodal model, with a better loss function. The sigmoid loss looks at image-text pairs on their own and needs no global view of all pairwise similarities for normalization, which the card says allows larger batch sizes and better results at smaller ones. The SoViT-400m architecture is the shape-optimized design from the paper "Getting ViT in Shape".
running_it: The card says images are resized to 384 by 384 and normalized with a mean and standard deviation of 0.5, and that texts are tokenized and padded to 64 tokens. Its example turns the scores into probabilities with a sigmoid.
fingerprint:
  attention_type: MHA
  num_layers: 27
  attention_heads: 16
  kv_heads: 16
  head_size: 72
  hidden_size: 1152
  intermediate_size: 4304
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: zero-shot-image-classification
  library: transformers
hub_signals:
  downloads_30d: 747876
  likes: 705
  last_modified: '2024-09-26'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google/siglip-so400m-patch14-384
aliases:
- google/siglip-so400m-patch14-384
lineage:
  parents: []
  children: []
quotes:
  summary: SigLIP model pre-trained on WebLi at resolution 384x384.
  intended_use: You can use the raw model for tasks like zero-shot image classification and image-text retrieval.
  what_sets_it_apart: The sigmoid loss operates solely on image-text pairs and does not require a global view of the pairwise similarities for normalization.
  running_it: Texts are tokenized and padded to the same length (64 tokens).
tags:
- zero-shot-image-classification
- siglip
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google/siglip-so400m-patch14-384
  title: google/siglip-so400m-patch14-384 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google/siglip-so400m-patch14-384/blob/main/config.json
  title: google/siglip-so400m-patch14-384 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2303.15343
  title: arXiv:2303.15343
  trust: 0.95
- url: https://arxiv.org/abs/2305.13035
  title: arXiv:2305.13035
  trust: 0.95
- url: https://arxiv.org/abs/2209.06794
  title: arXiv:2209.06794
  trust: 0.95
---

## Summary

SigLIP is a model that matches images with text, so an image and a caption can be compared by how well they fit. This checkpoint is the shape-optimized SoViT-400m version, pre-trained on the WebLI dataset at 384 by 384 pixel resolution, from the paper "Sigmoid Loss for Language Image Pre-Training" and the Google big_vision repository. The team behind it wrote no model card, so the Hugging Face team wrote this one.

## Intended Use

The card says the raw model can be used for zero-shot image classification, where an image is scored against label texts supplied at run time without extra training, and for image-text retrieval. It points to the model hub for versions fine-tuned for other tasks.

## What Sets It Apart

The card describes SigLIP as CLIP, a multimodal model, with a better loss function. The sigmoid loss looks at image-text pairs on their own and needs no global view of all pairwise similarities for normalization, which the card says allows larger batch sizes and better results at smaller ones. The SoViT-400m architecture is the shape-optimized design from the paper "Getting ViT in Shape".

## Running It

The card says images are resized to 384 by 384 and normalized with a mean and standard deviation of 0.5, and that texts are tokenized and padded to 64 tokens. Its example turns the scores into probabilities with a sigmoid.
