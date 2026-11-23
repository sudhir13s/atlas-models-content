---
id: model/minimax-h3
type: model
slug: minimax-h3
schema_version: 3.0
title: MiniMax-H3
organization: organization/minimaxai
release_date: '2026-07-28'
license_note: minimax-h3-community-license-agreement
model_category: generation
model_kind: diffusion
tasks:
- task/video-generation
total_parameters: 33122992896
summary: MiniMax H3 is a video generation system from MiniMax that also produces sound. According to the model card it accepts text, images, video and audio as input and generates video with native stereo audio, in clips of 4 to 15 seconds and at up to 2K resolution. It is described as a general-purpose, omni-modal generative system made of three modules.
what_sets_it_apart: 'The card says the system has three modules: H3-Context-IR, which interprets the inputs and turns them into a form the generator understands, H3-Base, which produces video and audio at 768p, and H3-Regenerate-2K, which re-generates the result at 2K. The authors attribute its handling of complex multimodal instructions to a task-generalization-oriented system design.'
running_it: The card says H3-Context-IR relies on several hosted models and services and is not part of the open-source release. It offers an API that reproduces the official workflow and says developers can follow its Prompting Guidance to build their own preprocessing, and it strongly recommends including this step.
pipeline:
  source: https://huggingface.co/MiniMaxAI/MiniMax-H3
  summary: The figure shows H3-Base, the released part of the system, which makes video and stereo sound together at 768p. A Qwen3-VL encoder reads the prompt (left); one transformer cleans random video and audio latents step by step in a single packed sequence, and two decoders turn them into frames and sound. The hosted modules that prepare the prompt and regenerate at 2K are not part of the release.
  side_into: transformer
  branch_from: scheduler
  stages:
  - id: prompt
    role: input
    title: Text prompt
    details:
    - optional first or last frame
    note: The prompt is the structured description that H3-Context-IR, or a user following the prompting guide, writes. Images given as first or last frame are read by the encoder and the visual autoencoder.
    side: true
  - id: text-encoder
    role: textEncoder
    title: H3-Encoder
    details:
    - Qwen3-VL-32B · layer 50 · width 5120
    note: The full pretrained Qwen3-VL-32B reads the prompt, and the hidden states of its 50th layer go to the transformer.
    side: true
  - id: noise
    role: input
    title: Random noise
    details:
    - video and audio latents
    note: Generation starts from random video latents (24 channels, 16 times smaller in height and width, 4 times in time) and random audio latents (40 per second for each stereo channel).
  - id: pack
    role: step
    title: Pack into one sequence
    details:
    - video patches 1 × 2 × 2
    note: The video latents are cut into 1 by 2 by 2 patches, so each visual token covers 32 by 32 pixels and 4 frames. Video, audio and text tokens form one packed sequence with 3D rotary positions over time, height and width.
  - id: transformer
    role: denoiser
    title: H3-Omni-Transformer block
    details:
    - 56 heads · width 5376
    - single stream · 33B in all
    note: A dense single-stream transformer of 50 blocks predicts the velocity of the video and the audio latents together. Attention and feed-forward layers are shared by all modalities; only the input and output layers and the AdaLN branches are modality-specific.
    frame: Denoising loop
    inner_frame: H3 block
    inner_repeat: 50
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - separate video and audio schedules
    note: The video rows and the audio rows each move one step along their own schedule; conditioning rows such as a given first frame are never overwritten. The released weights are CFG-distilled.
    frame: Denoising loop
  - id: video-decoder
    role: autoencoder
    title: Video decoder
    details:
    - ViT-based
    note: The visual autoencoder's ViT-based decoder turns the video latents into frames.
  - id: video
    role: outputHead
    title: Video frames
    details:
    - 768p
  - id: audio-decoder
    role: autoencoder
    title: Audio decoder
    details:
    - per channel, same weights
    branch: true
    note: The audio decoder decodes the left and right channels separately with the same weights, and they are recombined into stereo.
  - id: audio
    role: outputHead
    title: Stereo audio
    details:
    - 32 kHz
    branch: true
filters:
  size_category: large
  is_gated: false
  pipeline_tag: image-text-to-video
  library: minimax-h3
hub_signals:
  downloads_30d: 5752082
  likes: 7280
  last_modified: '2026-11-20'
  observed_at: '2026-11-21'
  source: https://huggingface.co/MiniMaxAI/MiniMax-H3
aliases:
- MiniMaxAI/MiniMax-H3
lineage:
  parents: []
  children: []
quotes:
  summary: MiniMax H3 is a general-purpose, omni-modal generative system.
  what_sets_it_apart: Thanks to its task-generalization-oriented system design
  running_it: H3-Context-IR is critical to the quality of the final output, so we strongly recommend incorporating it into your generation pipeline
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/MiniMaxAI/MiniMax-H3
  title: MiniMaxAI/MiniMax-H3 — model page (Hugging Face)
  trust: 0.9
- url: https://github.com/huggingface/diffusers/blob/main/src/diffusers/modular_pipelines/minimax_h3/denoise.py
  title: 'diffusers/modular_pipelines/minimax_h3/denoise.py: denoising loop (Hugging Face Diffusers)'
  trust: 0.9
- url: https://huggingface.co/MiniMaxAI/MiniMax-H3/blob/main/transformer/config.json
  title: MiniMaxAI/MiniMax-H3 — transformer/config.json
  trust: 0.9
- url: https://huggingface.co/MiniMaxAI/MiniMax-H3/blob/main/model_index.json
  title: MiniMaxAI/MiniMax-H3 — model_index.json
  trust: 0.9
---


