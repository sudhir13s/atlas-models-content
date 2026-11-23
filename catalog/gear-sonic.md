---
id: model/gear-sonic
type: model
slug: gear-sonic
schema_version: 1.1
title: GEAR-SONIC
organization: organization/nvidia
release_date: '2026-02-12'
release_year: 2026
release_month: 2
license_note: nvidia-open-model-license
model_category: robotics-and-control
tasks:
- task/reinforcement-learning
summary: GEAR-SONIC, called SONIC (Supersizing Motion Tracking) in its card, is a humanoid behavior foundation model from NVIDIA. It is a control policy for humanoid robots, the part of the robot's software that chooses motor actions, learned from large-scale human motion data. The card says one policy handles walking, running, crawling, jumping and manipulation, and the repository provides three controller variants for the Unitree G1 robot.
intended_use: The card names general-purpose motion tracking, planning and teleoperation (controlling a robot remotely, here through a VR headset) for the default variant. It lists a low-latency variant for responsive teleoperation and for running vision-language-action (VLA) policies, and SONIC v1.1 for heading-stable whole-body teleoperation and VLA policies built on it.
what_sets_it_apart: NVIDIA says that instead of building separate controllers for predefined motions, SONIC uses motion tracking as a scalable training task, so a single unified policy produces many behaviors. All variants use 64-dimensional universal motion tokens and a decoder with 29 degrees of freedom (independent joint movements). A kinematic planner generates locomotion in several movement styles in real time.
running_it: 'The card lists how much motion reference each variant needs: 10 frames at 20 ms spacing (about 200 ms) for the default and v1.1 variants, and 4 frames (about 80 ms) for the low-latency one. It says each ONNX encoder must be used with the decoder and observation config from the same variant, and that inference uses TensorRT on desktop and Jetson hardware.'
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: reinforcement-learning
hub_signals:
  downloads_30d: 2852
  likes: 91
  last_modified: '2026-08-26'
  observed_at: '2026-11-21'
  source: https://huggingface.co/nvidia/GEAR-SONIC
aliases:
- nvidia/GEAR-SONIC
lineage:
  parents: []
  children: []
quotes:
  summary: (Supersizing Motion Tracking) is a humanoid behavior foundation model developed by NVIDIA
  intended_use: General-purpose motion tracking, planning, and teleoperation.
  what_sets_it_apart: Rather than building separate controllers for predefined motions, SONIC uses motion tracking as a scalable training task
  running_it: 4 frames at 20 ms spacing, approximately 80 ms
tags:
- reinforcement-learning
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/GEAR-SONIC
  title: nvidia/GEAR-SONIC — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/GEAR-SONIC/blob/main/config.json
  title: nvidia/GEAR-SONIC — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2511.07820
  title: arXiv:2511.07820
  trust: 0.95
---

## Summary

GEAR-SONIC, called SONIC (Supersizing Motion Tracking) in its card, is a humanoid behavior foundation model from NVIDIA. It is a control policy for humanoid robots, the part of the robot's software that chooses motor actions, learned from large-scale human motion data. The card says one policy handles walking, running, crawling, jumping and manipulation, and the repository provides three controller variants for the Unitree G1 robot.

## Intended Use

The card names general-purpose motion tracking, planning and teleoperation (controlling a robot remotely, here through a VR headset) for the default variant. It lists a low-latency variant for responsive teleoperation and for running vision-language-action (VLA) policies, and SONIC v1.1 for heading-stable whole-body teleoperation and VLA policies built on it.

## What Sets It Apart

NVIDIA says that instead of building separate controllers for predefined motions, SONIC uses motion tracking as a scalable training task, so a single unified policy produces many behaviors. All variants use 64-dimensional universal motion tokens and a decoder with 29 degrees of freedom (independent joint movements). A kinematic planner generates locomotion in several movement styles in real time.

## Running It

The card lists how much motion reference each variant needs: 10 frames at 20 ms spacing (about 200 ms) for the default and v1.1 variants, and 4 frames (about 80 ms) for the low-latency one. It says each ONNX encoder must be used with the decoder and observation config from the same variant, and that inference uses TensorRT on desktop and Jetson hardware.
