---
id: model/ace-step1-5
type: model
slug: ace-step1-5
schema_version: 1.1
title: Ace-Step1.5
organization: organization/ace-step
release_date: '2026-01-23'
release_year: 2026
release_month: 1
license: license/mit
model_category: generation
tasks:
- task/music-and-sound-generation
context_window_tokens: 32768
summary: ACE-Step 1.5 is an open music generation model from ACE-Step that turns a text description into a song, a task called text-to-music. Its model card says it pairs a language model, which plans the song, with a diffusion transformer, which produces the audio, and that it follows prompts in more than 50 languages. The card also states that it generates a full song in under 2 seconds on an A100 graphics card and runs locally with less than 4 GB of video memory.
intended_use: 'The model card presents it for creators: it says the generated music may be used for commercial purposes, and that the model fits the workflows of music artists, producers and content creators. Besides writing a song from a text prompt, the card lists editing functions such as cover generation, repainting and vocal-to-background-music conversion.'
what_sets_it_apart: The card describes a hybrid design in which a language model acts as a planner. It turns a simple request into a song blueprint, from a short loop to a 10-minute composition, writing metadata, lyrics and captions that then guide the diffusion transformer (the part that generates the audio). The card says the two are aligned through reinforcement learning that uses only the model's own internal signals, with no external reward model or human preference data. It describes the training data as licensed tracks, royalty-free or public-domain music, and synthetic audio made from MIDI files.
fingerprint:
  attention_type: GQA
  num_layers: 24
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  context_window_tokens: 32768
  vocabulary_size: 64003
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
pipeline:
  source: https://arxiv.org/abs/2602.00744
  summary: 'Two models work in turn. A language model plans the song: it writes metadata, lyrics and a caption, and a sequence of coarse audio codes. A diffusion transformer (DiT) then renders the sound from those codes in a few steps while reading the caption and lyrics (left), and a 1D autoencoder turns the latents into a 48 kHz stereo waveform.'
  side_into: dit
  stages:
  - id: caption
    role: input
    title: Caption + lyrics
    details:
    - written or refined by the planner
    side: true
  - id: condition-encoders
    role: textEncoder
    title: Condition encoders
    details:
    - captions · Qwen3-Embedding-0.6B
    - lyrics 8 layers · timbre 4 layers
    note: The caption embeddings from Qwen3-0.6B are joined with the outputs of a lyric encoder and a timbre encoder, and the DiT reads them through cross-attention.
    side: true
  - id: request
    role: input
    title: Song request
  - id: planner
    role: attention
    title: 5 Hz language model planner
    details:
    - 1.7B · from Qwen3-1.7B
    note: The language model first writes chain-of-thought metadata such as tempo, key, duration and structure in YAML, then lyrics, a caption and audio codes at 5 codes per second.
  - id: codes
    role: step
    title: Audio codes as source
    details:
    - 5 Hz codes · codebook about 64k
    note: The finite-scalar-quantized codes are the structural source latent; they fix melody and rhythm for the DiT.
  - id: join
    role: projector
    title: Join source, noise, mask
    details:
    - patchified · 25 Hz to 12.5 Hz
    note: The source latent, the noisy target and a mask are joined into one input. A patchify layer halves the sequence length to 12.5 positions per second. The mask lets one model generate, repaint or cover.
    frame: Denoising loop
  - id: dit
    role: denoiser
    title: Diffusion transformer layer
    details:
    - 16 heads · 8 KV heads · width 2048
    - about 2B parameters in all
    note: Odd layers use sliding-window attention over 128 positions for local detail and even layers use global grouped-query attention for long-range structure; cross-attention reads the caption, lyric and timbre vectors.
    frame: Denoising loop
    inner_frame: DiT layer
    inner_repeat: 24
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - flow matching · 8 steps
    note: This turbo checkpoint was distilled from 50 to 8 steps with shift 3 and runs without classifier-free guidance.
    frame: Denoising loop
  - id: vae
    role: autoencoder
    title: 1D VAE decoder
    details:
    - 64-dim latents at 25 Hz
    note: The waveform autoencoder turns the 64-dimensional latents, 25 per second, back into audio; it compresses 1920 times.
  - id: audio
    role: outputHead
    title: Stereo music
    details:
    - 48 kHz
filters:
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-to-audio
  library: transformers
hub_signals:
  downloads_30d: 105775
  likes: 986
  last_modified: '2026-02-03'
  observed_at: '2026-11-21'
  source: https://huggingface.co/ACE-Step/Ace-Step1.5
aliases:
- ACE-Step/Ace-Step1.5
lineage:
  parents: []
  children: []
quotes:
  summary: a highly efficient open-source music foundation model designed to bring commercial-grade music generation to consumer hardware
  intended_use: versatile editing capabilities—such as cover generation, repainting, and vocal-to-BGM conversion
  what_sets_it_apart: the Language Model (LM) functions as an omni-capable planner
tags:
- text-to-audio
- acestep
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/ACE-Step/Ace-Step1.5
  title: ACE-Step/Ace-Step1.5 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/ACE-Step/Ace-Step1.5/blob/main/config.json
  title: ACE-Step/Ace-Step1.5 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2602.00744
  title: arXiv:2602.00744
  trust: 0.95
- url: https://huggingface.co/ACE-Step/Ace-Step1.5/blob/main/acestep-v15-turbo/config.json
  title: 'ACE-Step/Ace-Step1.5 — acestep-v15-turbo/config.json'
  trust: 0.9
---

## Summary

ACE-Step 1.5 is an open music generation model from ACE-Step that turns a text description into a song, a task called text-to-music. Its model card says it pairs a language model, which plans the song, with a diffusion transformer, which produces the audio, and that it follows prompts in more than 50 languages. The card also states that it generates a full song in under 2 seconds on an A100 graphics card and runs locally with less than 4 GB of video memory.

## Intended Use

The model card presents it for creators: it says the generated music may be used for commercial purposes, and that the model fits the workflows of music artists, producers and content creators. Besides writing a song from a text prompt, the card lists editing functions such as cover generation, repainting and vocal-to-background-music conversion.

## What Sets It Apart

The card describes a hybrid design in which a language model acts as a planner. It turns a simple request into a song blueprint, from a short loop to a 10-minute composition, writing metadata, lyrics and captions that then guide the diffusion transformer (the part that generates the audio). The card says the two are aligned through reinforcement learning that uses only the model's own internal signals, with no external reward model or human preference data. It describes the training data as licensed tracks, royalty-free or public-domain music, and synthetic audio made from MIDI files.
