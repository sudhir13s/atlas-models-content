---
id: model/hunyuan3d-2
type: model
slug: hunyuan3d-2
schema_version: 3.0
title: Hunyuan3D-2
organization: organization/tencent
release_date: '2025-01-20'
license_note: tencent-hunyuan-community
model_category: generation
model_kind: diffusion
tasks:
- task/3d-generation
summary: 'Hunyuan3D 2.0 is a 3D generation system from Tencent that creates high-resolution textured 3D assets, meaning 3D models with a surface shape and painted texture. The card tags it for image-to-3D and text-to-3D and lists English and Chinese. It has two parts: Hunyuan3D-DiT, a shape model that builds the bare geometry to match a condition image, and Hunyuan3D-Paint, a texture model.'
intended_use: The card's tagline is creating and manipulating 3D assets. It says the texture model works on generated meshes and on hand-crafted ones, and it describes a companion platform, Hunyuan3D-Studio, that lets professional and amateur users manipulate or animate their meshes.
what_sets_it_apart: The card describes a two-stage pipeline that first creates a bare mesh and then synthesizes a texture map for it, which it says separates the difficulty of shape from the difficulty of texture. The shape model is built on a scalable flow-based diffusion transformer. The authors report that the system surpasses the baselines they compared in geometry detail, condition alignment and texture quality.
running_it: 'The card lists three pretrained components in this repository: Hunyuan3D-DiT-v2-0, Hunyuan3D-Paint-v2-0 and Hunyuan3D-Delight-v2-0. It points to the GitHub repository for code, says texturing needs a custom rasterizer and differentiable renderer to be compiled, and describes a diffusers-like API for the shape and texture models. The card''s metadata marks the repository as disallowed in the European Union.'
pipeline:
  source: https://arxiv.org/abs/2501.12202
  summary: 'Two models run one after the other. Hunyuan3D-DiT makes the bare shape: it cleans random latent tokens step by step while reading the image features (left), and the ShapeVAE decoder turns the tokens into a mesh. Hunyuan3D-Paint then paints views of that mesh from the image, and they are baked into a texture map.'
  side_into: double-stream
  stages:
  - id: cond-image
    role: input
    title: Input image
    side: true
  - id: preprocess
    role: step
    title: Remove background
    details:
    - object centred on white
    note: The background is removed, and the object is resized and centred on a white background, so more of the image's resolution goes to the object.
    side: true
  - id: dino
    role: visionEncoder
    title: DINOv2 Giant encoder
    details:
    - 518 × 518 input · width 1536
    note: The last layer's patch tokens, including the head token, are the condition tokens the shape model reads.
    side: true
  - id: noise
    role: input
    title: Random latent tokens
    details:
    - up to 3072 tokens · 64 channels
    note: Shape generation starts from random numbers in the latent space of the ShapeVAE. The tokens have no position embedding; their content alone says where the shape is.
  - id: double-stream
    role: denoiser
    title: Double-stream blocks
    details:
    - 16 blocks · shape and image tokens
    note: In the 16 double-stream blocks of Hunyuan3D-DiT the shape tokens and the image tokens have separate weights and meet in one attention. Only the step modulates the blocks.
    frame: Denoising loop
  - id: single-stream
    role: denoiser
    title: Single-stream block
    details:
    - one joined sequence · width 1024
    note: The shape and image tokens are then joined and processed together by 32 single-stream blocks.
    frame: Denoising loop
    inner_frame: Single block
    inner_repeat: 32
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - flow matching · Euler
    note: A first-order Euler step moves the tokens towards a shape using the predicted velocity; the loop runs once per inference step.
    frame: Denoising loop
  - id: shape-decoder
    role: autoencoder
    title: ShapeVAE decoder
    details:
    - tokens to signed distances
    note: A transformer decoder turns the tokens into a signed distance function, which gives, for any point in space, its distance to the surface.
  - id: marching
    role: step
    title: Marching cubes
    details:
    - signed distances to a mesh
  - id: paint
    role: denoiser
    title: Hunyuan3D-Paint
    details:
    - 8 to 12 views of the mesh
    note: A multi-view diffusion model paints the mesh from 8 to 12 viewpoints. It reads normal and position maps of the mesh and a delighted copy of the input image, so lighting is not baked in.
  - id: bake
    role: step
    title: Texture baking
    details:
    - views to a texture map
  - id: output
    role: outputHead
    title: Textured 3D mesh
filters:
  is_gated: false
  pipeline_tag: image-to-3d
  library: hunyuan3d-2
hub_signals:
  downloads_30d: 366307
  likes: 1887
  last_modified: '2025-10-17'
  observed_at: '2026-11-21'
  source: https://huggingface.co/tencent/Hunyuan3D-2
aliases:
- tencent/Hunyuan3D-2
lineage:
  parents: []
  children: []
quotes:
  summary: We present Hunyuan3D 2.0, an advanced large-scale 3D synthesis system for generating high-resolution textured 3D assets.
  intended_use: also provides flexibility for texturing either generated or handcrafted meshes.
  what_sets_it_apart: Hunyuan3D 2.0 features a two-stage generation pipeline, starting with the creation of a bare mesh, followed by the
  running_it: We designed a diffusers-like API to use our shape generation model - Hunyuan3D-DiT and texture synthesis model -
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/tencent/Hunyuan3D-2
  title: tencent/Hunyuan3D-2 — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2501.12202
  title: arXiv:2501.12202
  trust: 0.95
- url: https://arxiv.org/abs/2411.02293
  title: arXiv:2411.02293
  trust: 0.95
- url: https://huggingface.co/tencent/Hunyuan3D-2/blob/main/hunyuan3d-dit-v2-0/config.yaml
  title: tencent/Hunyuan3D-2 — hunyuan3d-dit-v2-0/config.yaml
  trust: 0.9
---


