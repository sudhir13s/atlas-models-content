---
id: model/smolvla-base
type: model
slug: smolvla-base
schema_version: 1.1
title: smolvla_base
organization: organization/lerobot
release_date: '2025-06-01'
release_year: 2025
release_month: 6
license: license/apache-2-0
model_category: robotics-and-control
tasks:
- task/robotics
parameters_active_b: 0.45
total_parameters: 450046176
base_models:
- name: HuggingFaceTB/SmolVLM2-500M-Video-Instruct
  address: https://huggingface.co/HuggingFaceTB/SmolVLM2-500M-Video-Instruct
summary: 'SmolVLA is a compact vision-language-action (VLA) model: it takes camera images from several views, the robot''s state and an optional language instruction, and outputs continuous robot actions. The card says it is designed for affordable robotics, can be trained on a single GPU and deployed on consumer hardware. This checkpoint is published under the lerobot account and lists HuggingFaceTB/SmolVLM2-500M-Video-Instruct as its base model.'
intended_use: The card names its intended use as a base model to fine-tune on your own use case.
what_sets_it_apart: The card lists flow matching as the training objective and continuous actions as the action representation. It claims the model matches the performance of much larger vision-language-action models through community-driven data.
running_it: The card shows installing the lerobot package with its smolvla extra and loading the model through the SmolVLAPolicy class. For fine-tuning it points to the lerobot-train script, with settings such as bfloat16 precision and the number of training steps. Hosted inference is turned off for this repository.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: robotics
  library: lerobot
hub_signals:
  downloads_30d: 131725
  likes: 499
  last_modified: '2026-10-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/lerobot/smolvla_base
aliases:
- lerobot/smolvla_base
lineage:
  parents: []
  children: []
quotes:
  summary: SmolVLA is a compact, efficient Vision-Language-Action (VLA) model designed for affordable robotics, trainable on a single GPU and deployable on consumer hardware
  intended_use: Base model to fine tune on your specific use case
  what_sets_it_apart: while matching the performance of much larger VLAs through community-driven data.
  running_it: Use your trainer script (`lerobot-train`) for full training loops.
tags:
- robotics
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/lerobot/smolvla_base
  title: lerobot/smolvla_base — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/lerobot/smolvla_base/blob/main/config.json
  title: lerobot/smolvla_base — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2506.01844
  title: arXiv:2506.01844
  trust: 0.95
---

## Summary

SmolVLA is a compact vision-language-action (VLA) model: it takes camera images from several views, the robot's state and an optional language instruction, and outputs continuous robot actions. The card says it is designed for affordable robotics, can be trained on a single GPU and deployed on consumer hardware. This checkpoint is published under the lerobot account and lists HuggingFaceTB/SmolVLM2-500M-Video-Instruct as its base model.

## Intended Use

The card names its intended use as a base model to fine-tune on your own use case.

## What Sets It Apart

The card lists flow matching as the training objective and continuous actions as the action representation. It claims the model matches the performance of much larger vision-language-action models through community-driven data.

## Running It

The card shows installing the lerobot package with its smolvla extra and loading the model through the SmolVLAPolicy class. For fine-tuning it points to the lerobot-train script, with settings such as bfloat16 precision and the number of training steps. Hosted inference is turned off for this repository.
