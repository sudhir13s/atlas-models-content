---
id: model/stable-audio-open-1-0
type: model
slug: stable-audio-open-1-0
schema_version: 1.1
title: stable-audio-open-1.0
organization: organization/stabilityai
release_date: '2024-05-24'
release_year: 2024
release_month: 5
license_note: stable-audio-community
model_category: generation
tasks:
- task/music-and-sound-generation
total_parameters: 1213448210
summary: 'Stable Audio Open 1.0 is a text-to-audio model from Stability AI: you describe a sound or piece of music in words and it generates it. The card says it produces variable-length stereo audio of up to 47 seconds at 44.1 kHz from text prompts.'
intended_use: The card says the primary use is research and experimentation on AI-based music and audio generation. That includes research into the limits of generative models, and text-guided generation of music and audio by machine learning practitioners and artists. It says the model should not be used in downstream applications without further risk evaluation, or to create audio that creates hostile or alienating environments.
what_sets_it_apart: 'The card describes three components: an autoencoder that compresses waveforms into a manageable sequence length, a T5-based text embedding for text conditioning, and a transformer-based diffusion model working in the autoencoder''s compressed space. It says the training data is 486,492 recordings from Freesound and the Free Music Archive, all under CC0, CC BY or CC Sampling+ licenses.'
limitations: 'The card lists these limits: it cannot generate realistic vocals, it was trained on English descriptions and performs worse in other languages, it does not work equally well across music styles and cultures, and it is better at sound effects and field recordings than at music. It adds that prompt engineering may be needed to get good results.'
pipeline:
  source: https://arxiv.org/abs/2407.14358
  summary: The sound is made in the latent space of an audio autoencoder, not as raw samples. A T5 text encoder reads the prompt, a diffusion transformer (DiT) cleans random noise step by step while reading the prompt and the wanted length, and a decoder turns the result into a stereo waveform.
  side_into: dit
  stages:
  - id: prompt
    role: input
    title: Text prompt
    side: true
  - id: text-encoder
    role: textEncoder
    title: T5 text encoder
    details:
    - T5-base · 109M parameters
    note: A pretrained T5-base encoder turns the prompt into a sequence of vectors.
    side: true
  - id: timing
    role: embedding
    title: Add timing values
    details:
    - start and total seconds
    note: Two timing values tell the model how long the sound should be. The model is trained to fill the rest of its window with silence, which is trimmed afterwards.
    side: true
  - id: noise
    role: input
    title: Random noise
    details:
    - in the latent space · 64 channels
    note: Generation starts from random numbers in the latent space of the autoencoder. A window of 1024 latent frames covers about 47 seconds.
  - id: dit
    role: denoiser
    title: Diffusion transformer
    details:
    - 1057M parameters
    - attention · cross-attention · MLP
    note: Each block has self-attention, a cross-attention layer that reads the text and timing vectors, and a gated MLP. Timing and the step are also prepended to the input sequence.
    frame: Denoising loop
  - id: scheduler
    role: step
    title: Scheduler step
    details:
    - DPM-Solver++
    note: Uses the transformer's prediction to make the latents a little cleaner. The paper samples with 100 steps and a classifier-free guidance scale of 7.0.
    frame: Denoising loop
  - id: vae
    role: autoencoder
    title: VAE decoder
    details:
    - latents to waveform
    note: The decoder half of the autoencoder (156M parameters with the encoder) upsamples the latents with transposed strided convolutions back to raw audio.
  - id: audio
    role: outputHead
    title: Stereo audio
    details:
    - 44.1 kHz · up to 47 s
filters:
  size_category: small
  is_gated: true
  pipeline_tag: text-to-audio
  library: stable-audio-tools
hub_signals:
  downloads_30d: 23354
  likes: 2017
  last_modified: '2025-06-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/stabilityai/stable-audio-open-1.0
aliases:
- stabilityai/stable-audio-open-1.0
lineage:
  parents: []
  children: []
quotes:
  summary: generates variable-length (up to 47s) stereo audio at 44.1kHz from text prompts.
  intended_use: 'The primary use of Stable Audio Open is research and experimentation on AI-based music and audio generation, including:'
  what_sets_it_apart: 'It comprises three components: an autoencoder that compresses waveforms into a manageable sequence length, a T5-based text embedding for text conditioning, and a transformer-based diffusion (DiT) model that operates in the latent space of the autoencoder.'
  limitations: The model is not able to generate realistic vocals.
tags:
- text-to-audio
updated: '2026-11-23'
sources:
- url: https://huggingface.co/stabilityai/stable-audio-open-1.0
  title: stabilityai/stable-audio-open-1.0 — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2407.14358
  title: arXiv:2407.14358
  trust: 0.95
---

## Summary

Stable Audio Open 1.0 is a text-to-audio model from Stability AI: you describe a sound or piece of music in words and it generates it. The card says it produces variable-length stereo audio of up to 47 seconds at 44.1 kHz from text prompts.

## Intended Use

The card says the primary use is research and experimentation on AI-based music and audio generation. That includes research into the limits of generative models, and text-guided generation of music and audio by machine learning practitioners and artists. It says the model should not be used in downstream applications without further risk evaluation, or to create audio that creates hostile or alienating environments.

## What Sets It Apart

The card describes three components: an autoencoder that compresses waveforms into a manageable sequence length, a T5-based text embedding for text conditioning, and a transformer-based diffusion model working in the autoencoder's compressed space. It says the training data is 486,492 recordings from Freesound and the Free Music Archive, all under CC0, CC BY or CC Sampling+ licenses.

## Limitations

The card lists these limits: it cannot generate realistic vocals, it was trained on English descriptions and performs worse in other languages, it does not work equally well across music styles and cultures, and it is better at sound effects and field recordings than at music. It adds that prompt engineering may be needed to get good results.
