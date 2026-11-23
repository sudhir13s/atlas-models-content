---
id: model/openvla-7b
type: model
slug: openvla-7b
schema_version: 1.1
title: openvla-7b
organization: organization/openvla
release_date: '2024-06-10'
release_year: 2024
release_month: 6
license: license/mit
model_category: robotics-and-control
tasks:
- task/robotics
parameters_active_b: 7.54
total_parameters: 7541237184
base_models:
- name: prism-dinosiglip-224px
  address: https://github.com/TRI-ML/prismatic-vlms
summary: 'OpenVLA 7B is a vision-language-action model: it takes a language instruction and a camera image and outputs actions for a robot. The card says it was trained on 970K robot manipulation episodes from the Open X-Embodiment dataset by the OpenVLA team, researchers from Stanford, UC Berkeley, Google DeepMind and the Toyota Research Institute. It was fine-tuned from prism-dinosiglip-224px, a vision-language model built on DINOv2 and SigLIP vision backbones and the Llama-2 language model.'
intended_use: The card says it can be used zero-shot to control robots for the combinations of robot types and domains seen in its pretraining data, for example a Widow-X robot in BridgeV2 environments, and can be fine-tuned for new tasks and robot setups with minimal demonstration data. Out of scope is zero-shot control of new robot types or setups missing from the pretraining mix; the card suggests collecting demonstrations and fine-tuning instead.
what_sets_it_apart: The card says it supports controlling multiple robots out of the box and can be adapted to new robot domains through parameter-efficient fine-tuning. Each prediction is a normalized 7-degree-of-freedom end-effector change, covering x, y, z, roll, pitch, yaw and the gripper.
running_it: 'The card says predicted actions must be un-normalized using statistics computed for each robot and dataset before they run on a real robot. Its example loads the model in bfloat16 on a CUDA GPU with trust_remote_code, optionally with flash attention, and uses a prompt of the form "In: What action should the robot take to {instruction}?" followed by "Out:".'
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: robotics
  library: transformers
hub_signals:
  downloads_30d: 63387
  likes: 267
  last_modified: '2026-02-17'
  observed_at: '2026-11-21'
  source: https://huggingface.co/openvla/openvla-7b
aliases:
- openvla/openvla-7b
lineage:
  parents: []
  children: []
quotes:
  summary: is an open vision-language-action model trained on 970K robot manipulation episodes
  intended_use: OpenVLA models can be used zero-shot to control robots for specific combinations of embodiments and domains seen in the Open-X pretraining mixture
  what_sets_it_apart: It supports controlling multiple robots out-of-the-box, and can be quickly adapted for new robot domains via (parameter-efficient) fine-tuning.
  running_it: actions need to be *un-normalized* subject to statistics computed on a per-robot,
tags:
- robotics
- openvla
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openvla/openvla-7b
  title: openvla/openvla-7b — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openvla/openvla-7b/blob/main/config.json
  title: openvla/openvla-7b — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2406.09246
  title: arXiv:2406.09246
  trust: 0.95
---

## Summary

OpenVLA 7B is a vision-language-action model: it takes a language instruction and a camera image and outputs actions for a robot. The card says it was trained on 970K robot manipulation episodes from the Open X-Embodiment dataset by the OpenVLA team, researchers from Stanford, UC Berkeley, Google DeepMind and the Toyota Research Institute. It was fine-tuned from prism-dinosiglip-224px, a vision-language model built on DINOv2 and SigLIP vision backbones and the Llama-2 language model.

## Intended Use

The card says it can be used zero-shot to control robots for the combinations of robot types and domains seen in its pretraining data, for example a Widow-X robot in BridgeV2 environments, and can be fine-tuned for new tasks and robot setups with minimal demonstration data. Out of scope is zero-shot control of new robot types or setups missing from the pretraining mix; the card suggests collecting demonstrations and fine-tuning instead.

## What Sets It Apart

The card says it supports controlling multiple robots out of the box and can be adapted to new robot domains through parameter-efficient fine-tuning. Each prediction is a normalized 7-degree-of-freedom end-effector change, covering x, y, z, roll, pitch, yaw and the gripper.

## Running It

The card says predicted actions must be un-normalized using statistics computed for each robot and dataset before they run on a real robot. Its example loads the model in bfloat16 on a CUDA GPU with trust_remote_code, optionally with flash attention, and uses a prompt of the form "In: What action should the robot take to {instruction}?" followed by "Out:".
