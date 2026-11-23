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
pipeline:
  source: https://arxiv.org/abs/2406.09246
  summary: A camera image is read by two vision encoders whose features are joined (left), and a small projector maps them into the language model's embedding space. A Llama 2 7B language model reads them with the text instruction and writes seven action tokens, which are turned back into a 7-dimensional robot action.
  side_into: merge
  stages:
  - id: image
    role: input
    title: Camera image
    details:
    - 224 × 224 pixels
    side: true
  - id: vision-encoders
    role: visionEncoder
    title: DINOv2 + SigLIP encoders
    details:
    - ViT-L/14 · SO400M/14 · 600M total
    note: The same image patches go through both encoders separately, and the two feature vectors of each patch are joined channel-wise.
    side: true
  - id: projector
    role: projector
    title: Projector
    details:
    - 2-layer MLP · GELU
    note: Maps the joined visual features into the language model's embedding space.
    side: true
  - id: instruction
    role: tokenizer
    title: Instruction tokens
    details:
    - Llama tokenizer
  - id: embedding
    role: embedding
    title: Token embedding
  - id: merge
    role: embedding
    title: Join image and text
    details:
    - one input sequence
    note: The projected image patches and the instruction embeddings form one sequence for the language model.
  - id: llm
    role: attention
    title: Llama 2 language model
    details:
    - 7B parameters
    note: The language model backbone of the Prismatic-7B vision-language model, fine-tuned to predict actions as text.
  - id: action-tokens
    role: outputHead
    title: Output head
    details:
    - one token per action dimension
    note: The 256 least used tokens of the Llama vocabulary are overwritten with action tokens, one per bin.
  - id: detokenize
    role: step
    title: Action de-tokenizer
    details:
    - 256 bins per dimension
    note: Each token is mapped back to a value; the bins evenly split the range between the 1st and 99th percentile of that dimension in the training data.
  - id: action
    role: outputHead
    title: Robot action
    details:
    - 7 dimensions
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
