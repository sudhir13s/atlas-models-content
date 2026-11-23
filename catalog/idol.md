---
id: model/idol
type: model
slug: idol
schema_version: 1.1
title: idol
organization: organization/speridlabs
release_date: '2026-10-28'
release_year: 2026
release_month: 10
license: license/apache-2-0
model_category: generation
tasks:
- task/3d-generation
summary: Idol is an image-to-3D model from speridlabs. From a single picture of an object it generates a 3D Gaussian splat, a 3D object stored as a cloud of small colored blobs called Gaussians, up to 1,048,576 of them, including the sides the picture never showed. The card says it runs on consumer GPUs, using 7.2 GB of video memory, and can run in under 2 seconds.
intended_use: 'The card says to give it a render, a generated image or a photo of one object. It removes the background, frames the object and writes the result as a standard 3DGS .ply file, which any Gaussian splat viewer can open. Two modes set the crop: synthetic for renders and generated images, and real for photographs. Running it requires access to two gated models on Hugging Face, a DINOv3 image encoder and the FLUX.2 [dev] model, of which only the VAE (the image compressor and decompressor) is downloaded.'
what_sets_it_apart: 'The card lists the parts of a full request: a diffusion transformer (DiT) that acts as the denoiser, an octree decoder, a Gaussian decoder, a background-removal network called BiRefNet, a DINOv3 ViT-H+/16 image encoder and the FLUX.2 VAE, about 1.80B parameters in total. It also reports that its default faster sampling, which compiles the denoiser and batches the guided and unguided passes, is about 1.7 times faster with the same output quality.'
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: image-to-3d
  library: idol
hub_signals:
  downloads_30d: 38718
  likes: 2638
  last_modified: '2026-10-29'
  observed_at: '2026-11-21'
  source: https://huggingface.co/speridlabs/idol
aliases:
- speridlabs/idol
lineage:
  parents: []
  children: []
quotes:
  summary: It runs on consumer GPUs, using 7.2 GB of VRAM and can run in under 2 seconds.
  intended_use: 'Give it a render, a generated image or a photo: it removes the background, frames the object'
  what_sets_it_apart: Idol reads its input through two gated models on Hugging Face.
tags:
- image-to-3d
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/speridlabs/idol
  title: speridlabs/idol — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/speridlabs/idol/blob/main/config.json
  title: speridlabs/idol — config.json
  trust: 0.9
---

## Summary

Idol is an image-to-3D model from speridlabs. From a single picture of an object it generates a 3D Gaussian splat, a 3D object stored as a cloud of small colored blobs called Gaussians, up to 1,048,576 of them, including the sides the picture never showed. The card says it runs on consumer GPUs, using 7.2 GB of video memory, and can run in under 2 seconds.

## Intended Use

The card says to give it a render, a generated image or a photo of one object. It removes the background, frames the object and writes the result as a standard 3DGS .ply file, which any Gaussian splat viewer can open. Two modes set the crop: synthetic for renders and generated images, and real for photographs. Running it requires access to two gated models on Hugging Face, a DINOv3 image encoder and the FLUX.2 [dev] model, of which only the VAE (the image compressor and decompressor) is downloaded.

## What Sets It Apart

The card lists the parts of a full request: a diffusion transformer (DiT) that acts as the denoiser, an octree decoder, a Gaussian decoder, a background-removal network called BiRefNet, a DINOv3 ViT-H+/16 image encoder and the FLUX.2 VAE, about 1.80B parameters in total. It also reports that its default faster sampling, which compiles the denoiser and batches the guided and unguided passes, is about 1.7 times faster with the same output quality.
