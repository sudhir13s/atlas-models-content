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
pipeline:
  source: https://arxiv.org/abs/2506.01844
  summary: Camera images (left), the text instruction and the robot's state become one sequence of tokens, which the first 16 layers of the small vision-language model SmolVLM2 read. An action expert, trained with flow matching, turns random noise into a chunk of 50 actions while reading those layers.
  side_into: merge
  stages:
  - id: images
    role: input
    title: Camera images
    details:
    - up to 3 cameras · padded to 512
    side: true
  - id: vision-encoder
    role: visionEncoder
    title: SmolVLM2 vision encoder
    details:
    - frozen · global image only
    note: The vision encoder of SmolVLM2-500M reads each image whole, without tiling.
    side: true
  - id: pixel-shuffle
    role: step
    title: Pixel shuffle
    details:
    - 64 tokens per frame
    note: Rearranges neighbouring visual features into fewer, wider tokens, limiting each image to 64 tokens.
    side: true
  - id: instruction
    role: tokenizer
    title: Instruction tokens
    details:
    - up to 48 tokens
  - id: embedding
    role: embedding
    title: Token embedding
  - id: state
    role: projector
    title: State token
    details:
    - linear layer · one token
    note: The robot's sensorimotor state is projected by a linear layer into a single token of the language model's width.
  - id: merge
    role: embedding
    title: Join image, text and state
    details:
    - one token sequence
  - id: vlm-block
    role: attention
    title: Attention + feed-forward
    details:
    - first 16 layers of SmolVLM2-500M
    note: Only the first half of the language model's layers is kept; the remaining layers are discarded to halve the cost.
    frame: VLM layer
    repeat: 16
  - id: self-attention
    role: attention
    title: Causal self-attention
    details:
    - among the action tokens
    note: The action expert starts from noisy actions and the flow time. Its self-attention layers let each action token see only the earlier tokens of the chunk.
    frame: Expert layer pair
    repeat: 8
  - id: cross-attention
    role: attention
    title: Cross-attention
    details:
    - reads the matching VLM layer
    note: The expert's cross-attention layers read the keys and values of the matching layer of the vision-language model. The expert's width is 0.75 times the model's.
    frame: Expert layer pair
  - id: flow-step
    role: step
    title: Flow-matching step
    details:
    - 10 steps at inference
    note: The expert predicts a velocity that moves the actions from noise towards the final chunk; inference runs it 10 times.
  - id: actions
    role: outputHead
    title: Action chunk
    details:
    - 50 actions
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
- url: https://github.com/huggingface/lerobot/blob/main/src/lerobot/policies/smolvla/smolvlm_with_expert.py
  title: 'lerobot/policies/smolvla/smolvlm_with_expert.py: reference implementation (Hugging Face LeRobot)'
  trust: 0.9
---

## Summary

SmolVLA is a compact vision-language-action (VLA) model: it takes camera images from several views, the robot's state and an optional language instruction, and outputs continuous robot actions. The card says it is designed for affordable robotics, can be trained on a single GPU and deployed on consumer hardware. This checkpoint is published under the lerobot account and lists HuggingFaceTB/SmolVLM2-500M-Video-Instruct as its base model.

## Intended Use

The card names its intended use as a base model to fine-tune on your own use case.

## What Sets It Apart

The card lists flow matching as the training objective and continuous actions as the action representation. It claims the model matches the performance of much larger vision-language-action models through community-driven data.

## Running It

The card shows installing the lerobot package with its smolvla extra and loading the model through the SmolVLAPolicy class. For fine-tuning it points to the lerobot-train script, with settings such as bfloat16 precision and the number of training steps. Hosted inference is turned off for this repository.
