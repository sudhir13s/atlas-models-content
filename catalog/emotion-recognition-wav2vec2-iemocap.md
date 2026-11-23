---
id: model/emotion-recognition-wav2vec2-iemocap
type: model
slug: emotion-recognition-wav2vec2-iemocap
schema_version: 1.1
title: emotion-recognition-wav2vec2-IEMOCAP
organization: organization/speechbrain
license: license/apache-2-0
model_category: audio
tasks:
- task/audio-classification
summary: 'This model recognizes emotion in speech: given an audio recording, it assigns an emotion label, a task called speech emotion recognition. It is published by SpeechBrain, an open-source speech toolkit, as a fine-tuned wav2vec2 (base) model trained on the training data of the IEMOCAP dataset. The card marks the language as English.'
intended_use: The card says the repository provides the tools needed to perform emotion recognition with the fine-tuned model through SpeechBrain, using a custom predictor class loaded with the foreign_class function.
running_it: The card says the system was trained on recordings sampled at 16 kHz with a single channel, and that the classify_file function automatically resamples audio and selects one channel when needed. It asks users to install the development version of SpeechBrain.
filters:
  is_gated: false
  pipeline_tag: audio-classification
  library: speechbrain
hub_signals:
  downloads_30d: 40825
  likes: 208
  last_modified: '2024-07-23'
  observed_at: '2026-11-21'
  source: https://huggingface.co/speechbrain/emotion-recognition-wav2vec2-IEMOCAP
aliases:
- speechbrain/emotion-recognition-wav2vec2-IEMOCAP
lineage:
  parents: []
  children: []
quotes:
  summary: It is trained on IEMOCAP training data.
  intended_use: This repository provides all the necessary tools to perform emotion recognition with a fine-tuned wav2vec2 (base) model using SpeechBrain.
  running_it: The system is trained with recordings sampled at 16kHz (single channel).
tags:
- audio-classification
updated: '2026-11-23'
sources:
- url: https://huggingface.co/speechbrain/emotion-recognition-wav2vec2-IEMOCAP
  title: speechbrain/emotion-recognition-wav2vec2-IEMOCAP — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2106.04624
  title: arXiv:2106.04624
  trust: 0.95
---

## Summary

This model recognizes emotion in speech: given an audio recording, it assigns an emotion label, a task called speech emotion recognition. It is published by SpeechBrain, an open-source speech toolkit, as a fine-tuned wav2vec2 (base) model trained on the training data of the IEMOCAP dataset. The card marks the language as English.

## Intended Use

The card says the repository provides the tools needed to perform emotion recognition with the fine-tuned model through SpeechBrain, using a custom predictor class loaded with the foreign_class function.

## Running It

The card says the system was trained on recordings sampled at 16 kHz with a single channel, and that the classify_file function automatically resamples audio and selects one channel when needed. It asks users to install the development version of SpeechBrain.
