---
id: model/hunyuan3d-2
type: model
slug: hunyuan3d-2
schema_version: 1.1
title: Hunyuan3D-2
organization: organization/tencent
release_date: '2025-01-20'
release_year: 2025
release_month: 1
license_note: tencent-hunyuan-community
model_category: generation
tasks:
- task/3d-generation
summary: 'Hunyuan3D 2.0 is a 3D generation system from Tencent that creates high-resolution textured 3D assets, meaning 3D models with a surface shape and painted texture. The card tags it for image-to-3D and text-to-3D and lists English and Chinese. It has two parts: Hunyuan3D-DiT, a shape model that builds the bare geometry to match a condition image, and Hunyuan3D-Paint, a texture model.'
intended_use: The card's tagline is creating and manipulating 3D assets. It says the texture model works on generated meshes and on hand-crafted ones, and it describes a companion platform, Hunyuan3D-Studio, that lets professional and amateur users manipulate or animate their meshes.
what_sets_it_apart: The card describes a two-stage pipeline that first creates a bare mesh and then synthesizes a texture map for it, which it says separates the difficulty of shape from the difficulty of texture. The shape model is built on a scalable flow-based diffusion transformer. The authors report that the system surpasses the baselines they compared in geometry detail, condition alignment and texture quality.
running_it: 'The card lists three pretrained components in this repository: Hunyuan3D-DiT-v2-0, Hunyuan3D-Paint-v2-0 and Hunyuan3D-Delight-v2-0. It points to the GitHub repository for code, says texturing needs a custom rasterizer and differentiable renderer to be compiled, and describes a diffusers-like API for the shape and texture models. The card''s metadata marks the repository as disallowed in the European Union.'
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
tags:
- image-to-3d
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
---

## Summary

Hunyuan3D 2.0 is a 3D generation system from Tencent that creates high-resolution textured 3D assets, meaning 3D models with a surface shape and painted texture. The card tags it for image-to-3D and text-to-3D and lists English and Chinese. It has two parts: Hunyuan3D-DiT, a shape model that builds the bare geometry to match a condition image, and Hunyuan3D-Paint, a texture model.

## Intended Use

The card's tagline is creating and manipulating 3D assets. It says the texture model works on generated meshes and on hand-crafted ones, and it describes a companion platform, Hunyuan3D-Studio, that lets professional and amateur users manipulate or animate their meshes.

## What Sets It Apart

The card describes a two-stage pipeline that first creates a bare mesh and then synthesizes a texture map for it, which it says separates the difficulty of shape from the difficulty of texture. The shape model is built on a scalable flow-based diffusion transformer. The authors report that the system surpasses the baselines they compared in geometry detail, condition alignment and texture quality.

## Running It

The card lists three pretrained components in this repository: Hunyuan3D-DiT-v2-0, Hunyuan3D-Paint-v2-0 and Hunyuan3D-Delight-v2-0. It points to the GitHub repository for code, says texturing needs a custom rasterizer and differentiable renderer to be compiled, and describes a diffusers-like API for the shape and texture models. The card's metadata marks the repository as disallowed in the European Union.
