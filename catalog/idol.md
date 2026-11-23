---
id: model/idol
type: model
slug: idol
schema_version: 3.0
title: idol
organization: organization/speridlabs
release_date: '2026-10-28'
license: license/apache-2-0
model_category: generation
model_kind: diffusion
tasks:
- task/3d-generation
summary: Idol is an image-to-3D model from speridlabs. From a single picture of an object it generates a 3D Gaussian splat, a 3D object stored as a cloud of small colored blobs called Gaussians, up to 1,048,576 of them, including the sides the picture never showed. The card says it runs on consumer GPUs, using 7.2 GB of video memory, and can run in under 2 seconds.
intended_use: 'The card says to give it a render, a generated image or a photo of one object. It removes the background, frames the object and writes the result as a standard 3DGS .ply file, which any Gaussian splat viewer can open. Two modes set the crop: synthetic for renders and generated images, and real for photographs. Running it requires access to two gated models on Hugging Face, a DINOv3 image encoder and the FLUX.2 [dev] model, of which only the VAE (the image compressor and decompressor) is downloaded.'
what_sets_it_apart: 'The card lists the parts of a full request: a diffusion transformer (DiT) that acts as the denoiser, an octree decoder, a Gaussian decoder, a background-removal network called BiRefNet, a DINOv3 ViT-H+/16 image encoder and the FLUX.2 VAE, about 1.80B parameters in total. It also reports that its default faster sampling, which compiles the denoiser and batches the guided and unguided passes, is about 1.7 times faster with the same output quality.'
pipeline:
  source: https://github.com/speridlabs/idol-sdk/blob/main/idol/pipeline.py
  summary: One picture of an object goes in and a 3D Gaussian splat comes out. Two image encoders read the picture (left); a transformer cleans random latent tokens and a camera token step by step while reading them, and two decoders turn the latent into anchor points and then into Gaussians.
  side_into: joint-blocks
  stages:
  - id: image
    role: input
    title: Input image
    side: true
  - id: preprocess
    role: step
    title: Remove background + crop
    details:
    - BiRefNet · 1024 × 1024
    note: BiRefNet removes the background and the object is cropped and framed. The crop leaves more room around the object for photos than for renders.
    side: true
  - id: encoders
    role: visionEncoder
    title: DINOv3 + FLUX.2 VAE
    details:
    - two sets of image tokens, added
    note: DINOv3 ViT-H+/16 gives one set of image tokens and the FLUX.2 autoencoder's encoder gives another; each is mapped to width 1024 and the two are added.
    side: true
  - id: context-refiner
    role: attention
    title: Image-token refiner
    details:
    - 2 blocks
    note: Two transformer blocks refine the image tokens on their own before they join the latent tokens; they run at every step.
    side: true
  - id: noise
    role: input
    title: Random noise
    details:
    - 8192 latent tokens + 1 camera
    note: Sampling starts from random numbers for 8192 latent tokens of 16 channels and one camera token of 5 values. The camera token ends as the predicted camera of the input picture.
  - id: noise-refiner
    role: attention
    title: Latent-token refiner
    details:
    - 2 blocks
    note: The latent tokens get fixed 3D positions and pass two blocks of their own before joining the image and camera tokens.
    frame: Denoising loop
  - id: joint-blocks
    role: denoiser
    title: Joint transformer block
    details:
    - 16 heads · width 1024
    note: Latent, image and camera tokens form one sequence, and each of the 24 blocks attends over all of them with learned rotary positions. The step modulates every block; the output is the velocity for the latent and the camera.
    frame: Denoising loop
    inner_frame: Joint block
    inner_repeat: 24
  - id: scheduler
    role: step
    title: Euler step
    details:
    - flow matching · 50 steps by default
    note: Moves the latent and camera tokens one step from noise towards data. The guided and unguided velocities are combined with a guidance strength of 3.0 by default.
    frame: Denoising loop
  - id: octree
    role: autoencoder
    title: Octree decoder
    details:
    - 4 blocks · places anchor points
    note: Reads the latent and places one anchor point for every 32 Gaussians requested, down to octree level 8.
  - id: gaussian-decoder
    role: autoencoder
    title: Gaussian decoder
    details:
    - 16 blocks · 32 Gaussians per anchor
    note: Reads each anchor point with the latent and turns it into 32 Gaussians, each with position, colour, opacity, scale and rotation.
  - id: output
    role: outputHead
    title: 3D Gaussian splat
    details:
    - 32,768 to 1,048,576 Gaussians
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
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/speridlabs/idol
  title: speridlabs/idol — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/speridlabs/idol/blob/main/config.json
  title: speridlabs/idol — config.json
  trust: 0.9
- url: https://github.com/speridlabs/idol-sdk/blob/main/idol/pipeline.py
  title: 'speridlabs/idol-sdk: idol/pipeline.py, inference pipeline (GitHub)'
  trust: 0.9
- url: https://github.com/speridlabs/idol-sdk/blob/main/idol/models/dit.py
  title: 'speridlabs/idol-sdk: idol/models/dit.py, denoiser (GitHub)'
  trust: 0.9
- url: https://github.com/speridlabs/idol-sdk/blob/main/idol/conditioning.py
  title: 'speridlabs/idol-sdk: idol/conditioning.py, image encoders (GitHub)'
  trust: 0.9
---


