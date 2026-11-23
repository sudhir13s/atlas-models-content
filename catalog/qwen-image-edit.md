---
id: model/qwen-image-edit
type: model
slug: qwen-image-edit
schema_version: 1.1
title: Qwen-Image-Edit
organization: organization/qwen
release_date: '2025-08-17'
release_year: 2025
release_month: 8
license: license/apache-2-0
model_category: generation
tasks:
- task/image-editing
total_parameters: 20430401088
summary: Qwen-Image-Edit is the image-editing version of Qwen-Image, from Qwen. It takes an input image and a written instruction and produces an edited image. The card says it is built on the 20B Qwen-Image model and lists English and Chinese as its languages.
intended_use: The card names two kinds of editing. Appearance editing adds, removes or changes elements while the rest of the image stays unchanged. Semantic editing, such as IP creation, object rotation and style transfer, may change many pixels but keeps the meaning consistent. It also lists bilingual text editing, which adds, deletes or changes text in images while keeping the original font, size and style.
what_sets_it_apart: 'Qwen says it extends the text rendering ability of Qwen-Image to editing. The input image is fed into two paths at once: Qwen2.5-VL for visual semantic control, and the VAE (variational autoencoder) encoder for visual appearance control.'
running_it: The card says to install the latest version of the Diffusers library from GitHub. Its example loads the model with QwenImageEditPipeline in bfloat16 on a CUDA GPU, with 50 inference steps and a true_cfg_scale of 4.0.
filters:
  size_category: large
  is_gated: false
  pipeline_tag: image-to-image
  library: diffusers
hub_signals:
  downloads_30d: 153157
  likes: 2587
  last_modified: '2025-08-25'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen-Image-Edit
aliases:
- Qwen/Qwen-Image-Edit
lineage:
  parents: []
  children: []
quotes:
  summary: We are excited to introduce Qwen-Image-Edit, the image editing version of Qwen-Image.
  intended_use: Qwen-Image-Edit supports bilingual (Chinese and English) text editing, allowing direct addition, deletion, and modification of text in images while preserving the original font, size, and style.
  what_sets_it_apart: Qwen-Image-Edit simultaneously feeds the input image into Qwen2.5-VL (for visual semantic control) and the VAE Encoder (for visual appearance control)
  running_it: Install the latest version of diffusers
tags:
- image-to-image
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen-Image-Edit
  title: Qwen/Qwen-Image-Edit — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2508.02324
  title: arXiv:2508.02324
  trust: 0.95
---

## Summary

Qwen-Image-Edit is the image-editing version of Qwen-Image, from Qwen. It takes an input image and a written instruction and produces an edited image. The card says it is built on the 20B Qwen-Image model and lists English and Chinese as its languages.

## Intended Use

The card names two kinds of editing. Appearance editing adds, removes or changes elements while the rest of the image stays unchanged. Semantic editing, such as IP creation, object rotation and style transfer, may change many pixels but keeps the meaning consistent. It also lists bilingual text editing, which adds, deletes or changes text in images while keeping the original font, size and style.

## What Sets It Apart

Qwen says it extends the text rendering ability of Qwen-Image to editing. The input image is fed into two paths at once: Qwen2.5-VL for visual semantic control, and the VAE (variational autoencoder) encoder for visual appearance control.

## Running It

The card says to install the latest version of the Diffusers library from GitHub. Its example loads the model with QwenImageEditPipeline in bfloat16 on a CUDA GPU, with 50 inference steps and a true_cfg_scale of 4.0.
