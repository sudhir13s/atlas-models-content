---
id: model/whisper-large-v3
type: model
slug: whisper-large-v3
schema_version: 1.1
title: whisper-large-v3
organization: organization/openai
release_date: '2023-11-07'
release_year: 2023
release_month: 11
license: license/apache-2-0
model_category: audio
tasks:
- task/speech-recognition
parameters_active_b: 1.54
total_parameters: 1543490560
summary: 'Whisper large-v3 is a speech recognition model from OpenAI: it transcribes spoken audio into text and can also translate speech into English. The card describes the Whisper paper by Radford et al. from OpenAI, and says Whisper was trained on more than 5 million hours of labeled data. Large-v3 has the same architecture as the large and large-v2 models, apart from small differences.'
intended_use: The card says the primary intended users are AI researchers studying the robustness, generalization and limits of the model, and that Whisper may also be useful as a speech recognition tool for developers, especially for English. It cautions against transcribing recordings of people made without their consent, against using it for subjective classification, and against high-risk decision-making uses.
what_sets_it_apart: 'The card lists two changes from large-v2: the input spectrogram uses 128 Mel frequency bins instead of 80, and there is a new language token for Cantonese. It says large-v3 was trained on 1 million hours of weakly labeled audio and 4 million hours of audio pseudo-labeled with large-v2.'
running_it: The card has a section on additional speed and memory improvements, covering chunked long-form transcription, torch.compile, Flash Attention 2 and PyTorch scaled dot-product attention.
fingerprint:
  attention_type: MHA
  num_layers: 32
  decoder_layers: 32
  attention_heads: 20
  kv_heads: 20
  head_size: 64
  hidden_size: 1280
  intermediate_size: 5120
  vocabulary_size: 51866
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: pre
  stack: encoder-decoder
  input_stem: spectrogram-convolution
  positional_encoding: Sinusoidal
  decoder_positional_encoding: Learned
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: automatic-speech-recognition
  library: transformers
hub_signals:
  downloads_30d: 3303148
  likes: 6832
  last_modified: '2024-08-12'
  observed_at: '2026-11-21'
  source: https://huggingface.co/openai/whisper-large-v3
aliases:
- openai/whisper-large-v3
lineage:
  parents: []
  children:
  - model/whisper-large-v3-turbo
quotes:
  summary: Whisper large-v3 has the same architecture as the previous
  intended_use: The primary intended users of these models are AI researchers studying robustness, generalization, capabilities, biases, and constraints of the current model.
  what_sets_it_apart: The spectrogram input uses 128 Mel frequency bins instead of 80
  running_it: Additional Speed & Memory Improvements
tags:
- automatic-speech-recognition
- whisper
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openai/whisper-large-v3
  title: openai/whisper-large-v3 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/whisper-large-v3/blob/main/config.json
  title: openai/whisper-large-v3 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2212.04356
  title: arXiv:2212.04356
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/whisper/modeling_whisper.py
  title: 'transformers/models/whisper/modeling_whisper.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Whisper large-v3 is a speech recognition model from OpenAI: it transcribes spoken audio into text and can also translate speech into English. The card describes the Whisper paper by Radford et al. from OpenAI, and says Whisper was trained on more than 5 million hours of labeled data. Large-v3 has the same architecture as the large and large-v2 models, apart from small differences.

## Intended Use

The card says the primary intended users are AI researchers studying the robustness, generalization and limits of the model, and that Whisper may also be useful as a speech recognition tool for developers, especially for English. It cautions against transcribing recordings of people made without their consent, against using it for subjective classification, and against high-risk decision-making uses.

## What Sets It Apart

The card lists two changes from large-v2: the input spectrogram uses 128 Mel frequency bins instead of 80, and there is a new language token for Cantonese. It says large-v3 was trained on 1 million hours of weakly labeled audio and 4 million hours of audio pseudo-labeled with large-v2.

## Running It

The card has a section on additional speed and memory improvements, covering chunked long-form transcription, torch.compile, Flash Attention 2 and PyTorch scaled dot-product attention.
