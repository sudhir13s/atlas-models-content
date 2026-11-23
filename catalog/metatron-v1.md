---
id: model/metatron-v1
type: model
slug: metatron-v1
schema_version: 1.1
title: Metatron-v1
organization: organization/nvidia
release_date: '2026-10-27'
release_year: 2026
release_month: 10
license_note: nvidia-open-model-license
model_category: robotics-and-control
tasks:
- task/reinforcement-learning
summary: Metatron v1 is a humanoid robot control model published by NVIDIA. It is a neural network that outputs joint targets for a Unitree G1 humanoid robot with 29 controlled joints, and the card says walking, scene interaction and recovery share one policy. It is distributed as a native PyTorch checkpoint and as ONNX exports that run in a web browser.
intended_use: The card names researchers and developers running interactive humanoid control in a browser as the use case, with browser applications that provide the physics, observations, task goals and control logic. It says the model also supports other simulators and real G1 robots through the native package, but that compatibility is not a safety certification or a claim of validation on hardware.
what_sets_it_apart: The card describes a MaskedMimic-style transformer student with a deterministic prior and decoder, exported to ONNX and run with ONNX Runtime Web on WebGPU or WebAssembly. One policy covers walking, scene interaction and recovery with no separate recovery adapter. The demo network takes 39 inputs, including the robot's current state, eight past state samples, masked future goals and scene-box context, and returns joint targets, stiffness and damping.
limitations: 'The card says the ONNX files are not complete demos on their own: they need the matching application''s observation and control interfaces, and the demo and racer exports are not interchangeable.'
pipeline:
  source: https://huggingface.co/nvidia/Metatron-v1/blob/main/G1/MODEL_CARD.md
  summary: The robot's current and recent state, together with partial goals such as future poses, a style, a walking velocity and nearby boxes, go into a six-layer transformer prior. It gives a 64-dimensional latent, which an action decoder turns into targets for the 29 joints of a Unitree G1 humanoid. A fixed proportional-derivative (PD) mapping converts these into motor commands.
  stages:
  - id: inputs
    role: input
    title: Robot state + goals
    details:
    - current and past state and actions
    - masked pose, style, velocity goals
    note: The policy reads current and historical robot state and actions, masked global and local future-pose targets, style samples, planar velocity commands and up to two scene-box tokens. It computes the robot's own body poses from the joint readings inside the policy.
  - id: prior-block
    role: attention
    title: Attention + feed-forward
    details:
    - 4 heads · width 1024
    note: At inference the learned prior replaces the posterior used in training, so no teacher is needed.
    frame: Prior block
    repeat: 6
  - id: latent
    role: step
    title: Spherical latent
    details:
    - 64 dimensions
    note: The prior's output is a point in a 64-dimensional latent space on a sphere.
  - id: decoder
    role: outputHead
    title: Action decoder
    details:
    - conditioned on the latent
    note: Turns the latent into 29 raw actions, one per controlled joint.
  - id: pd
    role: step
    title: PD mapping
    details:
    - raw actions to joint targets
    note: The configured G1 mapping converts the raw actions into joint position targets with stiffness and damping gains.
  - id: commands
    role: outputHead
    title: Motor commands
    details:
    - 29 joints · Unitree G1
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: reinforcement-learning
  library: protomotions
hub_signals:
  downloads_30d: 1421
  likes: 18
  last_modified: '2026-11-07'
  observed_at: '2026-11-21'
  source: https://huggingface.co/nvidia/Metatron-v1
aliases:
- nvidia/Metatron-v1
lineage:
  parents: []
  children: []
quotes:
  summary: ONNX inference exports of the unified Metatron G1 scene/recovery policy
  intended_use: Interactive humanoid control, locomotion and recovery evaluation with the
  what_sets_it_apart: scene interaction and recovery share one policy, with no separate recovery adapter.
  limitations: the ONNX files alone are not complete demos
tags:
- reinforcement-learning
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/Metatron-v1
  title: nvidia/Metatron-v1 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/Metatron-v1/blob/main/config.json
  title: nvidia/Metatron-v1 — config.json
  trust: 0.9
- url: https://huggingface.co/nvidia/Metatron-v1/blob/main/G1/MODEL_CARD.md
  title: nvidia/Metatron-v1 — G1 native model card (Hugging Face)
  trust: 0.9
---

## Summary

Metatron v1 is a humanoid robot control model published by NVIDIA. It is a neural network that outputs joint targets for a Unitree G1 humanoid robot with 29 controlled joints, and the card says walking, scene interaction and recovery share one policy. It is distributed as a native PyTorch checkpoint and as ONNX exports that run in a web browser.

## Intended Use

The card names researchers and developers running interactive humanoid control in a browser as the use case, with browser applications that provide the physics, observations, task goals and control logic. It says the model also supports other simulators and real G1 robots through the native package, but that compatibility is not a safety certification or a claim of validation on hardware.

## What Sets It Apart

The card describes a MaskedMimic-style transformer student with a deterministic prior and decoder, exported to ONNX and run with ONNX Runtime Web on WebGPU or WebAssembly. One policy covers walking, scene interaction and recovery with no separate recovery adapter. The demo network takes 39 inputs, including the robot's current state, eight past state samples, masked future goals and scene-box context, and returns joint targets, stiffness and damping.

## Limitations

The card says the ONNX files are not complete demos on their own: they need the matching application's observation and control interfaces, and the demo and racer exports are not interchangeable.
