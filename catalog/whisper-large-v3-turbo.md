---
id: model/whisper-large-v3-turbo
type: model
slug: whisper-large-v3-turbo
schema_version: 1.1
title: whisper-large-v3-turbo
organization: organization/openai
release_date: '2024-10-01'
release_year: 2024
release_month: 10
license: license/mit
model_category: audio
tasks:
- task/speech-recognition
parameters_active_b: 0.81
total_parameters: 808878080
summary: 'Whisper large-v3-turbo is a speech recognition model from OpenAI: it turns spoken audio into text, a task called automatic speech recognition (ASR), and can translate speech into English. The card says it is a finetuned version of a pruned Whisper large-v3, the same model except that the decoding layers are cut from 32 to 4, which makes it faster at the expense of a minor quality degradation.'
intended_use: The card says Whisper models are primarily trained and evaluated on speech recognition and on speech translation into English, with strong recognition results in about 10 languages. They may show other abilities, such as voice activity detection or speaker diarization after fine-tuning, but these were not robustly evaluated, and the card urges users to test in their own context before deploying.
what_sets_it_apart: The card describes Whisper as a Transformer encoder-decoder model trained on more than 5 million hours of labeled data, which generalizes zero-shot, meaning without extra training, to many datasets and domains. This turbo version keeps the same encoder and reduces the decoder to 4 layers. By default the model predicts the source language automatically and transcribes, and setting the task to translate produces English text.
limitations: The card says the models were trained on large-scale noisy data in a weakly supervised way, so output may include text that was not spoken, a failure called hallucination. They perform unevenly across languages, accents and dialects, and the card says they are prone to repetitive text.
fingerprint:
  num_layers: 32
  hidden_size: 1280
  vocabulary_size: 51866
  activation_function: gelu
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: automatic-speech-recognition
  library: transformers
hub_signals:
  downloads_30d: 4713890
  likes: 3562
  last_modified: '2024-10-04'
  observed_at: '2026-11-21'
  source: https://huggingface.co/openai/whisper-large-v3-turbo
aliases:
- openai/whisper-large-v3-turbo
lineage:
  parents:
  - model/whisper-large-v3
  children: []
quotes:
  summary: Whisper large-v3-turbo is a finetuned version of a pruned
  intended_use: The models are primarily trained and evaluated on ASR and speech translation to English tasks.
  what_sets_it_apart: the number of decoding layers have reduced from 32 to 4
  limitations: the predictions may include texts that are not actually spoken in the audio input
tags:
- automatic-speech-recognition
- whisper
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/openai/whisper-large-v3-turbo
  title: openai/whisper-large-v3-turbo — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/openai/whisper-large-v3-turbo/blob/main/config.json
  title: openai/whisper-large-v3-turbo — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2212.04356
  title: arXiv:2212.04356
  trust: 0.95
---

## Summary

Whisper large-v3-turbo is a speech recognition model from OpenAI: it turns spoken audio into text, a task called automatic speech recognition (ASR), and can translate speech into English. The card says it is a finetuned version of a pruned Whisper large-v3, the same model except that the decoding layers are cut from 32 to 4, which makes it faster at the expense of a minor quality degradation.

## Intended Use

The card says Whisper models are primarily trained and evaluated on speech recognition and on speech translation into English, with strong recognition results in about 10 languages. They may show other abilities, such as voice activity detection or speaker diarization after fine-tuning, but these were not robustly evaluated, and the card urges users to test in their own context before deploying.

## What Sets It Apart

The card describes Whisper as a Transformer encoder-decoder model trained on more than 5 million hours of labeled data, which generalizes zero-shot, meaning without extra training, to many datasets and domains. This turbo version keeps the same encoder and reduces the decoder to 4 layers. By default the model predicts the source language automatically and transcribes, and setting the task to translate produces English text.

## Limitations

The card says the models were trained on large-scale noisy data in a weakly supervised way, so output may include text that was not spoken, a failure called hallucination. They perform unevenly across languages, accents and dialects, and the card says they are prone to repetitive text.
