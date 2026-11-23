---
id: model/stable-fast-3d
type: model
slug: stable-fast-3d
schema_version: 1.1
title: stable-fast-3d
organization: organization/stabilityai
release_date: '2024-07-17'
release_year: 2024
release_month: 7
license_note: stabilityai-ai-community
model_category: generation
tasks:
- task/3d-generation
total_parameters: 1006027436
summary: Stable Fast 3D (SF3D) is a large reconstruction model from Stability AI that takes a single image of an object and generates a textured, UV-unwrapped 3D mesh asset, a 3D surface with a texture laid out flat. The card says it creates the 3D model in under one second and expects a 512 by 512 pixel input. The repository is gated behind a license agreement.
intended_use: The card lists generation of artworks and use in design and other artistic processes, educational or creative tools, and research on reconstruction models. It says the model was not trained to give factual or true representations of people or events, so using it for that is out of scope, and that all uses should follow Stability AI's Acceptable Use Policy.
what_sets_it_apart: The card says the asset has a relatively low polygon count and goes through a delighting step, which makes it easier to use in game engines and rendering. The model also predicts per-object material parameters (roughness and metallic) to improve reflective behavior. It was trained on renders of the Objaverse dataset made with an enhanced rendering method that the card says better matches the distribution of real-world images.
running_it: The card says the model expects a 512 by 512 pixel input and refers to the GitHub repository for usage instructions. Its Community License is free for research and for commercial use by those with annual revenue under US$1 million, and larger organizations need an enterprise license from Stability AI.
pipeline:
  source: https://arxiv.org/abs/2408.00653
  summary: One image of an object goes in and a textured 3D mesh comes out, in a single pass with no denoising loop. A transformer turns DINOv2 image tokens into triplanes, three feature planes that describe the object in 3D; a mesh is extracted from them, and a separate network reads the material from the image (left).
  side_into: export
  stages:
  - id: material-image
    role: input
    title: The same image
    side: true
  - id: clip
    role: visionEncoder
    title: CLIP image encoder
    details:
    - frozen
    side: true
  - id: material-net
    role: feedForward
    title: Material Net
    details:
    - 2 MLPs · 3 hidden layers · width 512
    note: Predicts one metallic and one roughness value for the whole object, as the most likely value of a predicted Beta distribution.
    side: true
  - id: image
    role: input
    title: Input image
    details:
    - one object
  - id: dino
    role: visionEncoder
    title: DINOv2 image encoder
    details:
    - image tokens
    note: Turns the image into the tokens the transformer reads.
  - id: read
    role: attention
    title: Latents read triplane
    details:
    - cross-attention
    note: A fixed-size set of latent tokens fetches information from the triplane tokens, so the cost grows only linearly with the number of triplane tokens.
    frame: Two-stream unit
    repeat: 4
  - id: process
    role: attention
    title: Process latent tokens
    details:
    - main computation
    frame: Two-stream unit
  - id: write
    role: attention
    title: Update triplane tokens
    details:
    - from the latent tokens
    frame: Two-stream unit
  - id: shuffle
    role: step
    title: Pixel shuffle
    details:
    - 96² × 1024 → 384² × 40
    note: Rearranges the channels of the 96 by 96 triplanes into finer planes of 384 by 384 with 40 channels, to reduce aliasing in textures.
  - id: mesh
    role: step
    title: Mesh extraction
    details:
    - DMTet · vertex offsets · normals
    note: Differentiable marching tetrahedra turn the triplanes into a mesh. Two small MLP heads predict an offset for each vertex and a world-space normal, giving smoother surfaces.
  - id: export
    role: step
    title: UV unwrap + export
    details:
    - albedo and normal textures
    note: The mesh is UV-unwrapped, world positions are baked to the texture atlas and albedo and normals are queried for every texel; the material values are attached. The paper gives 150 ms for this step.
  - id: output
    role: outputHead
    title: Textured 3D mesh
filters:
  size_category: small
  is_gated: true
  pipeline_tag: image-to-3d
hub_signals:
  downloads_30d: 26570
  likes: 1580
  last_modified: '2025-04-08'
  observed_at: '2026-11-21'
  source: https://huggingface.co/stabilityai/stable-fast-3d
aliases:
- stabilityai/stable-fast-3d
lineage:
  parents: []
  children: []
quotes:
  summary: Stable Fast 3D (SF3D) is a large reconstruction model which takes in a single image of an object and generates a textured UV-unwrapped 3D mesh asset.
  intended_use: Generation of artworks and use in design and other artistic processes.
  what_sets_it_apart: The model also predicts per-object material parameters (roughness, metallic), enhancing reflective behaviors during rendering.
  running_it: The model expects an input size of 512x512 pixels.
tags:
- image-to-3d
updated: '2026-11-23'
sources:
- url: https://huggingface.co/stabilityai/stable-fast-3d
  title: stabilityai/stable-fast-3d — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2408.00653
  title: arXiv:2408.00653
  trust: 0.95
---

## Summary

Stable Fast 3D (SF3D) is a large reconstruction model from Stability AI that takes a single image of an object and generates a textured, UV-unwrapped 3D mesh asset, a 3D surface with a texture laid out flat. The card says it creates the 3D model in under one second and expects a 512 by 512 pixel input. The repository is gated behind a license agreement.

## Intended Use

The card lists generation of artworks and use in design and other artistic processes, educational or creative tools, and research on reconstruction models. It says the model was not trained to give factual or true representations of people or events, so using it for that is out of scope, and that all uses should follow Stability AI's Acceptable Use Policy.

## What Sets It Apart

The card says the asset has a relatively low polygon count and goes through a delighting step, which makes it easier to use in game engines and rendering. The model also predicts per-object material parameters (roughness and metallic) to improve reflective behavior. It was trained on renders of the Objaverse dataset made with an enhanced rendering method that the card says better matches the distribution of real-world images.

## Running It

The card says the model expects a 512 by 512 pixel input and refers to the GitHub repository for usage instructions. Its Community License is free for research and for commercial use by those with annual revenue under US$1 million, and larger organizations need an enterprise license from Stability AI.
