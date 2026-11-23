---
id: model/flux-3-action-base
type: model
slug: flux-3-action-base
schema_version: 3.0
title: flux-3-action-base
organization: organization/black-forest-labs
release_date: '2026-09-22'
license_note: flux-kommunity-license
model_category: robotics-and-control
model_kind: diffusion
tasks:
- task/robotics
summary: FLUX 3 Action Base is part of FLUX 3 Action, an open-weights 7-billion-parameter world action model from Black Forest Labs. Such a model takes camera frames, the robot's state and a text instruction, and returns the next chunk of actions, denoised together with the next video frames. This repository holds the action base weights and shared encoders used to adapt it to a new robot, and the card states it is not a complete robot policy.
intended_use: The card describes the weights as an adaptation component for training action heads for a new robot, simulator or game. It names as out of scope any use that breaks the law, controls a machine in a way that endangers people without human oversight and a means to stop it, or supports fully automated decisions with high impact on individuals.
what_sets_it_apart: The card says the base has video and text streams, a shared action trunk, and ee50 and gaming action heads, and that new embodiments need their own action heads. The shared text encoder is an unmodified copy of Qwen3-VL-4B-Instruct.
running_it: The card says the model outputs joint targets and nothing in it limits joint velocity, force or workspace, so the application must enforce those limits and keep a hardware stop within reach. It advises validating in a simulator or with the arm's safety limits engaged before running near people.
pipeline:
  source: https://github.com/black-forest-labs/flux-action/blob/main/src/flux_action/policy.py
  summary: Camera frames are compressed by a video autoencoder, and the robot's state becomes a conditioning token. The next video frames and the next chunk of actions start as noise and are denoised together by one diffusion transformer that also reads the text instruction (left). This repository holds the shared trunk and frozen encoders; a robot needs its own trained action heads.
  side_into: joint-blocks
  stages:
  - id: instruction
    role: input
    title: Text instruction
    side: true
  - id: text-encoder
    role: textEncoder
    title: Qwen3-VL-4B text encoder
    details:
    - frozen · unmodified copy
    note: An unmodified copy of Qwen3-VL-4B-Instruct turns the instruction into a sequence of vectors that the transformer reads as context.
    side: true
  - id: cameras
    role: input
    title: Camera frames
    details:
    - composited onto one canvas
    note: The cameras are placed on one canvas in a fixed layout (for DROID, the wrist camera on top and the two exterior cameras below).
  - id: vae-encoder
    role: autoencoder
    title: Video VAE encoder
    details:
    - frames to latents
    note: The shared, frozen video autoencoder turns the canvas into latents, which condition the next frames.
  - id: join
    role: projector
    title: Join state and noise
    details:
    - state token · noisy frames + actions
    note: The measured robot state becomes the conditioning token of the action stream. The next video frames and the action chunk start as random noise.
    frame: Denoising loop
  - id: stream-blocks
    role: attention
    title: Per-stream blocks
    details:
    - video, action and text apart
    note: Each stream (video, action, text) first runs through its own attention and feed-forward blocks. The action branch is one shared action backbone for every embodiment.
    frame: Denoising loop
  - id: joint-blocks
    role: denoiser
    title: Joint blocks
    details:
    - all streams attend together
    note: The text context and the video and action tokens are joined into one sequence, and every token attends to every other in shared blocks.
    frame: Denoising loop
  - id: heads
    role: outputHead
    title: Output layers per stream
    details:
    - video and action predictions
    note: A final layer per stream gives the prediction for the next frames and for the actions. The action heads are specific to each robot; new embodiments train their own.
    frame: Denoising loop
  - id: sampler
    role: step
    title: Sampler step
    details:
    - DROID policy · UniPC, 4 steps
    note: Moves the video and action tokens a step towards clean values. The DROID checkpoint was benchmarked with four UniPC steps and guidance 4.0 on video only.
    frame: Denoising loop
  - id: actions
    role: outputHead
    title: Action chunk
    details:
    - joint targets · 32 per chunk (DROID)
filters:
  is_gated: true
  pipeline_tag: robotics
  library: lerobot
hub_signals:
  downloads_30d: 1045
  likes: 172
  last_modified: '2026-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/black-forest-labs/flux-3-action-base
aliases:
- black-forest-labs/flux-3-action-base
lineage:
  parents: []
  children: []
quotes:
  summary: FLUX 3 Action is an open weights 7B world action model.
  intended_use: This is an adaptation component, not a complete robot policy.
  what_sets_it_apart: The text encoder is an unmodified copy of Qwen/Qwen3-VL-4B-Instruct;
  running_it: the application must enforce those limits and keep a hardware stop within reach.
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/black-forest-labs/flux-3-action-base
  title: black-forest-labs/flux-3-action-base — model page (Hugging Face)
  trust: 0.9
- url: https://github.com/black-forest-labs/flux-action/blob/main/src/flux_action/policy.py
  title: 'flux-action/src/flux_action/policy.py: inference policy (Black Forest Labs)'
  trust: 0.9
- url: https://github.com/black-forest-labs/flux-action/blob/main/src/flux_action/models/transformer.py
  title: 'flux-action/src/flux_action/models/transformer.py: diffusion transformer (Black Forest Labs)'
  trust: 0.9
- url: https://github.com/black-forest-labs/flux-action/blob/main/src/flux_action/config.py
  title: 'flux-action/src/flux_action/config.py: policy settings (Black Forest Labs)'
  trust: 0.9
---


