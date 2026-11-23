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
---

## Summary

Metatron v1 is a humanoid robot control model published by NVIDIA. It is a neural network that outputs joint targets for a Unitree G1 humanoid robot with 29 controlled joints, and the card says walking, scene interaction and recovery share one policy. It is distributed as a native PyTorch checkpoint and as ONNX exports that run in a web browser.

## Intended Use

The card names researchers and developers running interactive humanoid control in a browser as the use case, with browser applications that provide the physics, observations, task goals and control logic. It says the model also supports other simulators and real G1 robots through the native package, but that compatibility is not a safety certification or a claim of validation on hardware.

## What Sets It Apart

The card describes a MaskedMimic-style transformer student with a deterministic prior and decoder, exported to ONNX and run with ONNX Runtime Web on WebGPU or WebAssembly. One policy covers walking, scene interaction and recovery with no separate recovery adapter. The demo network takes 39 inputs, including the robot's current state, eight past state samples, masked future goals and scene-box context, and returns joint targets, stiffness and damping.

## Limitations

The card says the ONNX files are not complete demos on their own: they need the matching application's observation and control interfaces, and the demo and racer exports are not interchangeable.
