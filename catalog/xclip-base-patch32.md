---
id: model/xclip-base-patch32
type: model
slug: xclip-base-patch32
schema_version: 1.1
title: xclip-base-patch32
organization: organization/microsoft
release_date: '2022-08-25'
release_year: 2022
release_month: 8
license: license/mit
model_category: vision
tasks:
- task/video-understanding
parameters_active_b: 0.2
total_parameters: 196585855
context_window_tokens: 77
summary: X-CLIP is a video-language model from Microsoft that scores how well a text fits a video. This is the base-sized version with a patch resolution of 32, trained fully supervised on the Kinetics-400 video dataset using 8 frames per video at 224 by 224 pixels. It comes from the paper "Expanding Language-Image Pretrained Models for General Video Recognition". The Hugging Face team wrote the card, because the X-CLIP team did not.
intended_use: The card says the raw model can be used to determine how well text goes with a given video, and points to the model hub for versions fine-tuned on other tasks. It lists zero-shot, few-shot and fully supervised video classification, and video-text retrieval, as tasks it enables.
what_sets_it_apart: The card describes X-CLIP as a minimal extension of CLIP, an image-text model, for general video-language understanding. It is trained in a contrastive way on pairs of a video and a text, so that matching pairs score higher than mismatched ones.
running_it: The card says training used 8 frames per video at 224 by 224 pixels. During validation, the shorter edge of each frame is resized, the frame is center-cropped to a fixed size and normalized with the ImageNet mean and standard deviation.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 8
  kv_heads: 8
  head_size: 64
  hidden_size: 512
  intermediate_size: 2048
  context_window_tokens: 77
  vocabulary_size: 49408
  activation_function: quick_gelu
  normalization: LayerNorm
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: video-classification
  library: transformers
hub_signals:
  downloads_30d: 61365
  likes: 126
  last_modified: '2024-02-04'
  observed_at: '2026-11-21'
  source: https://huggingface.co/microsoft/xclip-base-patch32
aliases:
- microsoft/xclip-base-patch32
lineage:
  parents: []
  children: []
quotes:
  summary: This model was trained using 8 frames per video, at a resolution of 224x224.
  intended_use: You can use the raw model for determining how well text goes with a given video.
  what_sets_it_apart: The model is trained in a contrastive way on (video, text) pairs.
  running_it: During validation, one resizes the shorter edge of each frame, after which center cropping is performed to a fixed-size resolution (like 224x224).
tags:
- video-classification
- xclip
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/microsoft/xclip-base-patch32
  title: microsoft/xclip-base-patch32 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/xclip-base-patch32/blob/main/config.json
  title: microsoft/xclip-base-patch32 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2208.02816
  title: arXiv:2208.02816
  trust: 0.95
---

## Summary

X-CLIP is a video-language model from Microsoft that scores how well a text fits a video. This is the base-sized version with a patch resolution of 32, trained fully supervised on the Kinetics-400 video dataset using 8 frames per video at 224 by 224 pixels. It comes from the paper "Expanding Language-Image Pretrained Models for General Video Recognition". The Hugging Face team wrote the card, because the X-CLIP team did not.

## Intended Use

The card says the raw model can be used to determine how well text goes with a given video, and points to the model hub for versions fine-tuned on other tasks. It lists zero-shot, few-shot and fully supervised video classification, and video-text retrieval, as tasks it enables.

## What Sets It Apart

The card describes X-CLIP as a minimal extension of CLIP, an image-text model, for general video-language understanding. It is trained in a contrastive way on pairs of a video and a text, so that matching pairs score higher than mismatched ones.

## Running It

The card says training used 8 frames per video at 224 by 224 pixels. During validation, the shorter edge of each frame is resized, the frame is center-cropped to a fixed size and normalized with the ImageNet mean and standard deviation.
