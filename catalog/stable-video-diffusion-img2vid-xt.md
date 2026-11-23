---
id: model/stable-video-diffusion-img2vid-xt
type: model
slug: stable-video-diffusion-img2vid-xt
schema_version: 1.1
title: stable-video-diffusion-img2vid-xt
organization: organization/stabilityai
release_date: '2023-11-20'
release_year: 2023
release_month: 11
license_note: stable-video-diffusion-community
model_category: generation
tasks:
- task/video-generation
total_parameters: 1524623082
summary: 'Stable Video Diffusion Image-to-Video XT is an image-to-video model from Stability AI: it takes a still image as the starting frame and generates a short video from it. The card says it was trained to generate 25 frames at 576 by 1024 resolution, and that it was fine-tuned from the 14-frame version of Stable Video Diffusion Image-to-Video.'
intended_use: The card lists research on generative models, safe deployment of models that could generate harmful content, studying the limits and biases of generative models, generating artworks and design material, and educational or creative tools. It says the model was not trained to be factual and that making factual depictions of people or events is out of scope.
what_sets_it_apart: The card says it is a latent diffusion model, which generates in a compressed image space, and that Stability AI also fine-tuned the f8 decoder for temporal consistency and provides a standard frame-wise decoder as an alternative.
limitations: 'The card lists these limits: videos are short (4 seconds or less) and not perfectly photorealistic, the model may produce videos with no motion or very slow camera pans, it cannot be controlled through text or render legible text, faces and people may not be generated properly, and its autoencoder is lossy.'
pipeline:
  source: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/stable_video_diffusion/pipeline_stable_video_diffusion.py
  summary: 'One still image becomes a short video. The image conditions the model in two ways: as latents joined to the noisy frames, and as one vector the U-Net reads. Random noise is cleaned step by step, and a decoder turns the result into frames.'
  side_into: unet
  stages:
  - id: image-vector
    role: input
    title: The same image
    side: true
  - id: image-encoder
    role: visionEncoder
    title: CLIP image encoder
    details:
    - one vector per image
    note: Turns the input image into one embedding vector. The U-Net reads it at every step of the loop.
    side: true
  - id: image
    role: input
    title: Input image
  - id: vae-encoder
    role: autoencoder
    title: VAE encoder
    details:
    - image to latents
    note: The encoder half of the autoencoder turns the input image into latents.
  - id: join
    role: step
    title: Join with noisy latents
    details:
    - one copy per video frame
    note: The frames start as random noise in the latent space. The image latents are repeated for every frame and joined to the noisy latents before each U-Net call.
    frame: Denoising loop
  - id: unet
    role: denoiser
    title: Spatio-temporal U-Net
    details:
    - predicts the noise in the latents
    note: Reads the joined latents, the step, the image vector and the frame rate and motion values, and predicts the noise for all frames.
    frame: Denoising loop
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - Euler discrete
    note: Uses the predicted noise to make the latents a little cleaner; the loop runs once per inference step.
    frame: Denoising loop
  - id: vae-decoder
    role: autoencoder
    title: Temporal VAE decoder
    details:
    - latents to frames
    note: Turns the cleaned latents into video frames, a few frames at a time.
  - id: video
    role: outputHead
    title: Video frames
filters:
  size_category: small
  is_gated: false
  pipeline_tag: image-to-video
  library: diffusers
hub_signals:
  downloads_30d: 224125
  likes: 3494
  last_modified: '2024-07-10'
  observed_at: '2026-11-21'
  source: https://huggingface.co/stabilityai/stable-video-diffusion-img2vid-xt
aliases:
- stabilityai/stable-video-diffusion-img2vid-xt
lineage:
  parents: []
  children: []
quotes:
  summary: Stable Video Diffusion (SVD) Image-to-Video is a diffusion model that takes in a still image as a conditioning frame, and generates a video from it.
  intended_use: Generation of artworks and use in design and other artistic processes.
  what_sets_it_apart: We also finetune the widely used
  limitations: The generated videos are rather short (<= 4sec), and the model does not achieve perfect photorealism.
tags:
- image-to-video
updated: '2026-11-23'
sources:
- url: https://huggingface.co/stabilityai/stable-video-diffusion-img2vid-xt
  title: stabilityai/stable-video-diffusion-img2vid-xt — model page (Hugging Face)
  trust: 0.9
- url: https://github.com/huggingface/diffusers/blob/main/src/diffusers/pipelines/stable_video_diffusion/pipeline_stable_video_diffusion.py
  title: 'diffusers/pipelines/stable_video_diffusion/pipeline_stable_video_diffusion.py: reference pipeline (Hugging Face Diffusers)'
  trust: 0.9
- url: https://huggingface.co/stabilityai/stable-video-diffusion-img2vid-xt/blob/main/model_index.json
  title: stabilityai/stable-video-diffusion-img2vid-xt — model_index.json
  trust: 0.9
---

## Summary

Stable Video Diffusion Image-to-Video XT is an image-to-video model from Stability AI: it takes a still image as the starting frame and generates a short video from it. The card says it was trained to generate 25 frames at 576 by 1024 resolution, and that it was fine-tuned from the 14-frame version of Stable Video Diffusion Image-to-Video.

## Intended Use

The card lists research on generative models, safe deployment of models that could generate harmful content, studying the limits and biases of generative models, generating artworks and design material, and educational or creative tools. It says the model was not trained to be factual and that making factual depictions of people or events is out of scope.

## What Sets It Apart

The card says it is a latent diffusion model, which generates in a compressed image space, and that Stability AI also fine-tuned the f8 decoder for temporal consistency and provides a standard frame-wise decoder as an alternative.

## Limitations

The card lists these limits: videos are short (4 seconds or less) and not perfectly photorealistic, the model may produce videos with no motion or very slow camera pans, it cannot be controlled through text or render legible text, faces and people may not be generated properly, and its autoencoder is lossy.
