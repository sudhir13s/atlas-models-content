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
