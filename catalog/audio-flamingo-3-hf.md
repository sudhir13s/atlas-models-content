---
id: model/audio-flamingo-3-hf
type: model
slug: audio-flamingo-3-hf
schema_version: 1.1
title: audio-flamingo-3-hf
organization: organization/nvidia
release_date: '2025-10-24'
release_year: 2025
release_month: 10
license_note: other
model_category: audio
tasks:
- task/speech-language-models
parameters_active_b: 8.27
total_parameters: 8267215360
context_window_tokens: 32768
summary: Audio Flamingo 3 is an audio-language model from NVIDIA. It takes audio, such as speech, sounds or music, together with a text instruction, and answers in text. The card lists English as its language, says it handles audio up to 10 minutes long (processed in 30-second windows), and shows it running through the Hugging Face Transformers library.
intended_use: The card says it is intended for researchers and developers exploring audio question answering and reasoning, long-context audio comprehension, interactive sound and music design assistants, and multi-turn voice chat. It states that the model is for non-commercial research purposes only.
what_sets_it_apart: NVIDIA lists the design choices behind it. One audio encoder, called AF-Whisper, is shared by speech, sound and music. A small adaptor network (an MLP, or multi-layer perceptron) passes the audio features to a decoder-only language model backbone based on Qwen2.5-7B. Chain-of-thought reasoning, where the model writes out intermediate steps, can be switched on when wanted. The card says it was developed from NVILA and Qwen2.5-7B, and it credits a variant called AF3-Chat with multi-turn conversation and voice-to-voice interaction.
running_it: The card notes that audio is processed in 30-second windows with a 10-minute total cap per sample, and that longer inputs are truncated. It lists a maximum of 16000 text tokens for input and 1024 text tokens for output.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 28
  kv_heads: 4
  head_size: 128
  hidden_size: 3584
  intermediate_size: 18944
  context_window_tokens: 32768
  vocabulary_size: 151672
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: audio-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 81378
  likes: 200
  last_modified: '2026-04-13'
  observed_at: '2026-11-21'
  source: https://huggingface.co/nvidia/audio-flamingo-3-hf
aliases:
- nvidia/audio-flamingo-3-hf
lineage:
  parents: []
  children: []
quotes:
  summary: advances reasoning and understanding across speech, sounds, and music
  intended_use: 'Intended for researchers and developers to explore:'
  what_sets_it_apart: Unified audio representation learning (speech, sound, music)
  running_it: AF3 processes audio in 30-second windows with a **10-minute** total cap per sample.
tags:
- audio-text-to-text
- audioflamingo3
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/audio-flamingo-3-hf
  title: nvidia/audio-flamingo-3-hf — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/audio-flamingo-3-hf/blob/main/config.json
  title: nvidia/audio-flamingo-3-hf — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2507.08128
  title: arXiv:2507.08128
  trust: 0.95
- url: https://arxiv.org/abs/2505.13032
  title: arXiv:2505.13032
  trust: 0.95
---

## Summary

Audio Flamingo 3 is an audio-language model from NVIDIA. It takes audio, such as speech, sounds or music, together with a text instruction, and answers in text. The card lists English as its language, says it handles audio up to 10 minutes long (processed in 30-second windows), and shows it running through the Hugging Face Transformers library.

## Intended Use

The card says it is intended for researchers and developers exploring audio question answering and reasoning, long-context audio comprehension, interactive sound and music design assistants, and multi-turn voice chat. It states that the model is for non-commercial research purposes only.

## What Sets It Apart

NVIDIA lists the design choices behind it. One audio encoder, called AF-Whisper, is shared by speech, sound and music. A small adaptor network (an MLP, or multi-layer perceptron) passes the audio features to a decoder-only language model backbone based on Qwen2.5-7B. Chain-of-thought reasoning, where the model writes out intermediate steps, can be switched on when wanted. The card says it was developed from NVILA and Qwen2.5-7B, and it credits a variant called AF3-Chat with multi-turn conversation and voice-to-voice interaction.

## Running It

The card notes that audio is processed in 30-second windows with a 10-minute total cap per sample, and that longer inputs are truncated. It lists a maximum of 16000 text tokens for input and 1024 text tokens for output.
