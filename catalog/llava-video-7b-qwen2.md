---
id: model/llava-video-7b-qwen2
type: model
slug: llava-video-7b-qwen2
schema_version: 1.1
title: LLaVA-Video-7B-Qwen2
organization: organization/lmms-lab
release_date: '2024-09-02'
release_year: 2024
release_month: 9
license: license/apache-2-0
model_category: vision
tasks:
- task/video-understanding
parameters_active_b: 8.03
total_parameters: 8030348832
context_window_tokens: 32768
base_models:
- name: lmms-lab/llava-onevision-qwen2-7b-si
  address: https://huggingface.co/lmms-lab/llava-onevision-qwen2-7b-si
summary: 'LLaVA-Video-7B-Qwen2 is a video-language model: it takes a video and a text question and answers in text. The card says the LLaVA-Video models are 7B and 72B parameter models trained on the LLaVA-Video-178K and LLaVA-OneVision datasets, based on the Qwen2 language model with a 32K-token context window. This checkpoint was initialized from lmms-lab/llava-onevision-qwen2-7b-si, and the card lists English and Chinese.'
intended_use: The card says the model can interact with images, several images and videos, but is specific to videos. It names no out-of-scope uses.
what_sets_it_apart: The card lists the architecture as SO400M, a vision encoder, combined with the Qwen2 language model. Training used a mixture of 1.6M single-image, multi-image and video examples for one epoch, updating the full model, in bfloat16 precision, on 256 Nvidia A100 GPUs (for the whole model series).
running_it: The card says the model supports at most 64 frames. Its example samples 64 frames evenly from the video, tells the model the video length and the time of each frame in the prompt, and decodes without sampling for up to 4096 new tokens.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 28
  kv_heads: 4
  head_size: 128
  hidden_size: 3584
  intermediate_size: 18944
  context_window_tokens: 32768
  vocabulary_size: 152064
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: video-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 12134
  likes: 127
  last_modified: '2024-10-25'
  observed_at: '2026-11-21'
  source: https://huggingface.co/lmms-lab/LLaVA-Video-7B-Qwen2
aliases:
- lmms-lab/LLaVA-Video-7B-Qwen2
lineage:
  parents: []
  children: []
quotes:
  summary: based on Qwen2 language model with a context window of 32K tokens.
  intended_use: having the ability to interact with images, multi-image and videos, but specific to videos.
  what_sets_it_apart: A mixture of 1.6M single-image/multi-image/video data, 1 epoch, full model
  running_it: This model support at most 64 frames.
tags:
- video-text-to-text
- llava
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/lmms-lab/LLaVA-Video-7B-Qwen2
  title: lmms-lab/LLaVA-Video-7B-Qwen2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/lmms-lab/LLaVA-Video-7B-Qwen2/blob/main/config.json
  title: lmms-lab/LLaVA-Video-7B-Qwen2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2410.02713
  title: arXiv:2410.02713
  trust: 0.95
---

## Summary

LLaVA-Video-7B-Qwen2 is a video-language model: it takes a video and a text question and answers in text. The card says the LLaVA-Video models are 7B and 72B parameter models trained on the LLaVA-Video-178K and LLaVA-OneVision datasets, based on the Qwen2 language model with a 32K-token context window. This checkpoint was initialized from lmms-lab/llava-onevision-qwen2-7b-si, and the card lists English and Chinese.

## Intended Use

The card says the model can interact with images, several images and videos, but is specific to videos. It names no out-of-scope uses.

## What Sets It Apart

The card lists the architecture as SO400M, a vision encoder, combined with the Qwen2 language model. Training used a mixture of 1.6M single-image, multi-image and video examples for one epoch, updating the full model, in bfloat16 precision, on 256 Nvidia A100 GPUs (for the whole model series).

## Running It

The card says the model supports at most 64 frames. Its example samples 64 frames evenly from the video, tells the model the video length and the time of each frame in the prompt, and decodes without sampling for up to 4096 new tokens.
