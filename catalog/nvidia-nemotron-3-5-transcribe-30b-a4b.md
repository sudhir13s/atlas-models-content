---
id: model/nvidia-nemotron-3-5-transcribe-30b-a4b
type: model
slug: nvidia-nemotron-3-5-transcribe-30b-a4b
schema_version: 1.1
title: NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
organization: organization/nvidia
release_date: '2026-10-07'
release_year: 2026
release_month: 10
license_note: openmdw-1.1
model_category: audio
tasks:
- task/speech-recognition
parameters_active_b: 33.7
total_parameters: 33697781029
base_models:
- name: nvidia/NVIDIA-Nemotron-3.5-Lightning-30B-A3B-BF16
  address: https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Lightning-30B-A3B-BF16
summary: 'NVIDIA Nemotron-3.5-Transcribe is an offline speech and audio model from NVIDIA: it takes a recording, plus a text instruction and optional context, and returns text. The card lists transcription, speech translation, speaker-attributed transcription, timestamp prediction, language identification and audio question answering, in 40 languages, for recordings of up to one hour.'
intended_use: The card names developers and researchers building speech interfaces, transcription and translation tools, meeting and media processing, customer-service analytics and audio-understanding applications. It says speaker labels distinguish speakers within a recording and do not establish real-world identity, and that it is an offline model rather than a real-time streaming service.
what_sets_it_apart: 'The card describes a Speech-Augmented Language Model: audio features from a transformer speech encoder and a speaker encoder are combined with the text prompt and fed to a language model that generates the text. The language model is a Mamba-2 and Transformer hybrid mixture of experts, and the card gives the whole model, audio encoder included, as 34 billion total parameters with 4.3 billion active. The task and style are chosen by the text instruction.'
running_it: The card says to choose the transcription style with the instruction in the user message and to keep the system prompt empty for transcription and translation. It recommends temperature 0 with reasoning off for transcription, translation and language identification, and temperature 0.6, top-p 0.95 with reasoning on for audio understanding. It lists one H100 80 GB GPU for deployment.
filters:
  size_category: large
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: automatic-speech-recognition
  library: nemo
hub_signals:
  downloads_30d: 92179
  likes: 3411
  last_modified: '2026-10-29'
  observed_at: '2026-11-21'
  source: https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
aliases:
- nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
lineage:
  parents: []
  children: []
quotes:
  summary: NVIDIA Nemotron-3.5-Transcribe is an offline speech and audio model that processes recordings, together with text instructions and optional context, into text responses.
  intended_use: Intended users are developers and researchers building speech interfaces, transcription and translation tools, meeting and media processing, customer-service analytics, and audio-understanding applications.
  what_sets_it_apart: Audio features are combined with the text prompt for text generation.
  running_it: Choose the transcription style with the instruction in the user message; keep the system prompt empty for transcription and translation.
tags:
- automatic-speech-recognition
- nemo-speechlm
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
  title: nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B/blob/main/config.json
  title: nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2310.09424
  title: arXiv:2310.09424
  trust: 0.95
---

## Summary

NVIDIA Nemotron-3.5-Transcribe is an offline speech and audio model from NVIDIA: it takes a recording, plus a text instruction and optional context, and returns text. The card lists transcription, speech translation, speaker-attributed transcription, timestamp prediction, language identification and audio question answering, in 40 languages, for recordings of up to one hour.

## Intended Use

The card names developers and researchers building speech interfaces, transcription and translation tools, meeting and media processing, customer-service analytics and audio-understanding applications. It says speaker labels distinguish speakers within a recording and do not establish real-world identity, and that it is an offline model rather than a real-time streaming service.

## What Sets It Apart

The card describes a Speech-Augmented Language Model: audio features from a transformer speech encoder and a speaker encoder are combined with the text prompt and fed to a language model that generates the text. The language model is a Mamba-2 and Transformer hybrid mixture of experts, and the card gives the whole model, audio encoder included, as 34 billion total parameters with 4.3 billion active. The task and style are chosen by the text instruction.

## Running It

The card says to choose the transcription style with the instruction in the user message and to keep the system prompt empty for transcription and translation. It recommends temperature 0 with reasoning off for transcription, translation and language identification, and temperature 0.6, top-p 0.95 with reasoning on for audio understanding. It lists one H100 80 GB GPU for deployment.
