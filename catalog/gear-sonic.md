---
id: model/gear-sonic
type: model
slug: gear-sonic
schema_version: 3.0
title: GEAR-SONIC
organization: organization/nvidia
release_date: '2026-02-12'
license_note: nvidia-open-model-license
model_category: robotics-and-control
model_kind: control-policy
tasks:
- task/reinforcement-learning
summary: GEAR-SONIC, called SONIC (Supersizing Motion Tracking) in its card, is a humanoid behavior foundation model from NVIDIA. It is a control policy for humanoid robots, the part of the robot's software that chooses motor actions, learned from large-scale human motion data. The card says one policy handles walking, running, crawling, jumping and manipulation, and the repository provides three controller variants for the Unitree G1 robot.
intended_use: The card names general-purpose motion tracking, planning and teleoperation (controlling a robot remotely, here through a VR headset) for the default variant. It lists a low-latency variant for responsive teleoperation and for running vision-language-action (VLA) policies, and SONIC v1.1 for heading-stable whole-body teleoperation and VLA policies built on it.
what_sets_it_apart: NVIDIA says that instead of building separate controllers for predefined motions, SONIC uses motion tracking as a scalable training task, so a single unified policy produces many behaviors. All variants use 64-dimensional universal motion tokens and a decoder with 29 degrees of freedom (independent joint movements). A kinematic planner generates locomotion in several movement styles in real time.
running_it: 'The card lists how much motion reference each variant needs: 10 frames at 20 ms spacing (about 200 ms) for the default and v1.1 variants, and 4 frames (about 80 ms) for the low-latency one. It says each ONNX encoder must be used with the decoder and observation config from the same variant, and that inference uses TensorRT on desktop and Jetson hardware.'
pipeline:
  source: https://arxiv.org/abs/2511.07820
  summary: A motion command (robot motion, human body poses or sparse teleoperation targets) goes into its own encoder and is quantized into a universal token. A control decoder reads the token together with the robot's own body state (left) and outputs the next joint commands for the 29 joints of the humanoid.
  side_into: control-decoder
  stages:
  - id: proprioception
    role: input
    title: Robot body state
    details:
    - joint and body readings
    note: The proprioceptive state, in the robot's local frame; the same inputs are used in simulation and on the real robot.
    side: true
  - id: command
    role: input
    title: Motion command
    details:
    - 10 future frames
    note: 'One of three kinds of command: robot motion, human SMPL poses or hybrid teleoperation targets. The kinematic planner can produce robot motion from gamepad or keyboard input.'
  - id: encoder
    role: feedForward
    title: Encoder per command type
    details:
    - MLP · 2048, 1024, 512, 512
    note: Each command type has its own MLP encoder. Training losses align the three encoders so they map the same motion to the same token.
  - id: quantizer
    role: step
    title: FSQ quantizer
    details:
    - 2 tokens · 32 levels × 32 dims
    note: Finite Scalar Quantization (FSQ) rounds the encoder output to fixed levels, giving the universal token of 64 numbers that all command types share.
  - id: control-decoder
    role: feedForward
    title: Control decoder
    details:
    - MLP · 8 layers, 4096 down to 512
    note: Reads the universal token joined with the body state. A second decoder, used only in training, rebuilds the robot motion from the token alone.
  - id: actions
    role: outputHead
    title: Joint commands
    details:
    - 29 joints · diagonal Gaussian
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
tags: []
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


