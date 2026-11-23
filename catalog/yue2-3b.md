---
id: model/yue2-3b
type: model
slug: yue2-3b
schema_version: 1.1
title: YuE2-3B
organization: organization/m-a-p
release_date: '2026-09-09'
release_year: 2026
release_month: 9
license_note: cc-by-nc-4.0
model_category: generation
tasks:
- task/music-and-sound-generation
parameters_active_b: 3.63
total_parameters: 3630684224
context_window_tokens: 24576
summary: YuE2-3B is an open music generation model, a text-to-audio model that writes songs. Given lyrics and a style prompt, it produces a complete song with vocals and accompaniment, and the song's melody and chords can then be shaped through an editable score. The card lists Chinese and English as its languages, and its license is CC BY-NC 4.0, which does not allow commercial use.
intended_use: 'The card shows three uses: creating a song from a style prompt and lyrics, making a cover that gives an existing recording a new arrangement, and editing the score, by hand or with an agent, before regenerating the audio. It recommends melody-only planning for covers.'
what_sets_it_apart: 'The card says the model plans with a symbolic score before making audio. One backbone that mixes autoregressive (step by step) and non-autoregressive transformers writes the score and semantic tokens, then generates acoustic latents through flow matching, and a separate VAE (variational autoencoder) turns them into stereo audio. It lists three planning modes: melody and chords, melody only, or no plan.'
running_it: The card lists Linux, Python 3.10 or later and a 24GB NVIDIA GPU with BF16 support as requirements, with 24GB of host RAM, producing 48 kHz stereo songs without quantization. It says the default planning mode is full melody and chords, and that a cfg_scale setting such as 1.2 can be tried for stronger text guidance.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 2048
  intermediate_size: 6144
  context_window_tokens: 24576
  vocabulary_size: 184704
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
pipeline:
  source: https://arxiv.org/abs/2609.33757
  summary: 'One 28-layer transformer does the work in two modes. In its autoregressive (AR) mode it writes a readable score in ABC notation and then semantic music tokens, one token at a time. In its non-autoregressive (NAR) mode it turns random acoustic latents into music by flow matching while reading the whole plan, and a separate autoencoder decodes them to 48 kHz stereo.'
  stages:
  - id: prompt
    role: input
    title: Style prompt + lyrics
  - id: tokenizer
    role: tokenizer
    title: Text tokenizer
    details:
    - byte-pair encoding
    note: Text, lyrics and the ABC score share the ordinary text tokenizer; the semantic codes use a separate range of the vocabulary.
  - id: score
    role: attention
    title: Write the ABC score
    details:
    - 28 layers · AR experts
    note: The AR stream predicts the score token by token after the text and lyrics; the score sets tempo, meter, key, chords and melody. For editing or covers a given score is used instead.
    frame: AR mode
  - id: semantic
    role: attention
    title: Write semantic tokens
    details:
    - 25 per second
    note: The AR stream continues with MERT2 semantic tokens, a dense musical trajectory at 25 tokens per second.
    frame: AR mode
  - id: noise
    role: input
    title: Random noise
    details:
    - acoustic latents · 25 Hz × 64
  - id: nar
    role: denoiser
    title: NAR stream
    details:
    - 28 layers · NAR experts
    note: Each of the 28 layers has separate normalisation, projections and MLP experts for each mode, and both modes take part in one attention computation. The NAR experts predict the flow velocity of the latents. Acoustic positions see the whole score and token sequence and each other; the AR positions never see the acoustic ones.
    frame: Denoising loop
  - id: step
    role: step
    title: Flow step
    details:
    - flow matching
    frame: Denoising loop
  - id: vae
    role: autoencoder
    title: VAE decoder
    details:
    - latents to waveform
    note: A separately trained decoder turns the 25-per-second, 64-dimensional latents into 48 kHz stereo audio.
  - id: audio
    role: outputHead
    title: Stereo song
    details:
    - 48 kHz
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-to-audio
hub_signals:
  downloads_30d: 20330
  likes: 1267
  last_modified: '2026-10-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/m-a-p/YuE2-3B
aliases:
- m-a-p/YuE2-3B
lineage:
  parents: []
  children: []
quotes:
  summary: Turn lyrics and a style prompt into a complete song with vocals and accompaniment, then shape its melody and chords through an editable score.
  intended_use: Start from an existing recording and give it a new arrangement.
  what_sets_it_apart: One AR–NAR Mixture-of-Transformers backbone writes the score and semantic tokens, then generates acoustic latents through flow matching.
  running_it: Linux · Python 3.10+ · 24GB NVIDIA GPU with BF16 support.
tags:
- text-to-audio
- yue2
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/m-a-p/YuE2-3B
  title: m-a-p/YuE2-3B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/m-a-p/YuE2-3B/blob/main/config.json
  title: m-a-p/YuE2-3B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2610.05336
  title: arXiv:2610.05336
  trust: 0.95
- url: https://arxiv.org/abs/2609.33757
  title: arXiv:2609.33757
  trust: 0.95
- url: https://arxiv.org/abs/2503.08638
  title: arXiv:2503.08638
  trust: 0.95
---

## Summary

YuE2-3B is an open music generation model, a text-to-audio model that writes songs. Given lyrics and a style prompt, it produces a complete song with vocals and accompaniment, and the song's melody and chords can then be shaped through an editable score. The card lists Chinese and English as its languages, and its license is CC BY-NC 4.0, which does not allow commercial use.

## Intended Use

The card shows three uses: creating a song from a style prompt and lyrics, making a cover that gives an existing recording a new arrangement, and editing the score, by hand or with an agent, before regenerating the audio. It recommends melody-only planning for covers.

## What Sets It Apart

The card says the model plans with a symbolic score before making audio. One backbone that mixes autoregressive (step by step) and non-autoregressive transformers writes the score and semantic tokens, then generates acoustic latents through flow matching, and a separate VAE (variational autoencoder) turns them into stereo audio. It lists three planning modes: melody and chords, melody only, or no plan.

## Running It

The card lists Linux, Python 3.10 or later and a 24GB NVIDIA GPU with BF16 support as requirements, with 24GB of host RAM, producing 48 kHz stereo songs without quantization. It says the default planning mode is full melody and chords, and that a cfg_scale setting such as 1.2 can be tried for stronger text guidance.
