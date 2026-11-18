---
id: task/3d-generation
type: task
slug: 3d-generation
schema_version: 1.0
title: "3D Generation"
group: task-group/generation
order: 4
summary: "Create a three-dimensional object or scene from a text prompt or from one or more images."
hf_pipeline_tags: [text-to-3d, image-to-3d]
inputs: [text, image]
outputs: [3d]
tags: [generative, 3d, mesh]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/text-to-3d", title: "Hugging Face — Text-to-3D task", trust: 0.9 }
---

## What It Is
A 3D generation model **produces an asset that can be viewed and rendered from any angle**.

- **Text-to-3D:** a written prompt describes the object to build.
- **Image-to-3D:** one or several photos are turned into a shape, with unseen sides inferred.
- **Meshes:** surfaces made of triangles, the format game engines and modelling tools expect.
- **Gaussian splats:** clouds of soft coloured points that render quickly but are harder to edit.

## When To Use
- **Use it** when the asset must be rotated, lit or placed inside a scene.
- **Use image generation instead** when a single fixed viewpoint is enough.
- **Use depth estimation instead** when you only need distance from the camera, not a full shape.

## How To Choose
- **Output format:** mesh, Gaussian splat or another representation, matched to your downstream tool.
- **Geometry quality:** clean surfaces and sensible topology matter when the asset will be edited.
- **Textures and materials:** whether colour and surface properties come with the shape.
- **Input type and speed:** text, one image or several views, and seconds or minutes per asset.
