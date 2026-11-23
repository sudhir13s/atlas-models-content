---
id: model/vibevoice-1-5b
type: model
slug: vibevoice-1-5b
schema_version: 1.1
title: VibeVoice-1.5B
organization: organization/microsoft
release_date: '2025-08-25'
release_year: 2025
release_month: 8
license: license/mit
model_category: audio
tasks:
- task/text-to-speech
parameters_active_b: 2.7
total_parameters: 2704021985
summary: VibeVoice-1.5B is a text-to-speech model from Microsoft that turns written text into spoken audio, aimed at long conversations with several speakers, such as podcasts. The card says it can produce up to 90 minutes of speech with up to 4 distinct speakers. It was trained on English and Chinese data only.
intended_use: 'The card limits the model to research use exploring realistic audio dialogue generation, and does not recommend commercial or real-world applications without further testing. It lists out-of-scope uses: voice impersonation without recorded consent, disinformation, real-time voice conversion, unsupported languages, music or background sound, and generating text transcripts.'
what_sets_it_apart: The card says it uses continuous speech tokenizers, an acoustic one and a semantic one, running at an ultra-low frame rate of 7.5 Hz, which makes long sequences cheaper to process. A large language model (LLM), Qwen2.5-1.5B in this release, reads the text and the dialogue flow, and a diffusion head generates the fine acoustic detail, a next-token diffusion framework.
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-to-speech
  library: transformers
hub_signals:
  downloads_30d: 214324
  likes: 2522
  last_modified: '2026-01-22'
  observed_at: '2026-11-21'
  source: https://huggingface.co/microsoft/VibeVoice-1.5B
aliases:
- microsoft/VibeVoice-1.5B
lineage:
  parents: []
  children: []
quotes:
  summary: generating expressive, long-form, multi-speaker conversational audio, such as podcasts, from text.
  intended_use: The VibeVoice model is limited to research purpose use exploring highly realistic audio dialogue generation
  what_sets_it_apart: VibeVoice employs a next-token diffusion framework, leveraging a Large Language Model (LLM) to understand textual context and dialogue flow, and a diffusion head to generate high-fidelity acoustic details.
tags:
- text-to-speech
- vibevoice
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/microsoft/VibeVoice-1.5B
  title: microsoft/VibeVoice-1.5B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/VibeVoice-1.5B/blob/main/config.json
  title: microsoft/VibeVoice-1.5B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2508.19205
  title: arXiv:2508.19205
  trust: 0.95
- url: https://arxiv.org/abs/2412.08635
  title: arXiv:2412.08635
  trust: 0.95
---

## Summary

VibeVoice-1.5B is a text-to-speech model from Microsoft that turns written text into spoken audio, aimed at long conversations with several speakers, such as podcasts. The card says it can produce up to 90 minutes of speech with up to 4 distinct speakers. It was trained on English and Chinese data only.

## Intended Use

The card limits the model to research use exploring realistic audio dialogue generation, and does not recommend commercial or real-world applications without further testing. It lists out-of-scope uses: voice impersonation without recorded consent, disinformation, real-time voice conversion, unsupported languages, music or background sound, and generating text transcripts.

## What Sets It Apart

The card says it uses continuous speech tokenizers, an acoustic one and a semantic one, running at an ultra-low frame rate of 7.5 Hz, which makes long sequences cheaper to process. A large language model (LLM), Qwen2.5-1.5B in this release, reads the text and the dialogue flow, and a diffusion head generates the fine acoustic detail, a next-token diffusion framework.
