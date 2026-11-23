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
pipeline:
  source: https://huggingface.co/speechbrain/emotion-recognition-wav2vec2-IEMOCAP/blob/main/hyperparams.yaml
  summary: A wav2vec2 base encoder turns the raw waveform into one vector every 20 ms. The vectors are averaged over the whole recording, and one linear layer scores four emotions.
  stages:
  - id: audio
    role: input
    title: Speech
    details:
    - 16 kHz · one channel
  - id: feature-encoder
    role: audioEncoder
    title: Convolution layers
    details:
    - 7 layers · 512 channels
    note: Seven convolution layers read the raw waveform with strides 5, 2, 2, 2, 2, 2 and 2, giving one vector per 320 samples, which is 20 ms of audio.
  - id: projection
    role: projector
    title: Feature projection
    details:
    - width 512 → 768
  - id: position
    role: positionalEncoding
    title: Convolutional positions
    details:
    - kernel 128 · 16 groups
    note: A convolution over the sequence adds information about position, instead of a table of position vectors.
  - id: encoder-block
    role: audioEncoder
    title: Attention + feed-forward
    details:
    - 12 heads · width 768
    frame: Encoder block
    repeat: 12
  - id: output-norm
    role: normalisation
    title: Output LayerNorm
  - id: pooling
    role: step
    title: Average over time
    details:
    - one vector per recording
    note: The mean of all the frame vectors, ignoring padding, gives one 768-number vector for the recording.
  - id: classifier
    role: outputHead
    title: Linear layer + softmax
    details:
    - neutral · angry · happy · sad
    note: One linear layer without bias gives four scores, and a softmax turns them into probabilities over the four emotion labels.
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
- url: https://huggingface.co/speechbrain/emotion-recognition-wav2vec2-IEMOCAP/blob/main/hyperparams.yaml
  title: speechbrain/emotion-recognition-wav2vec2-IEMOCAP — hyperparams.yaml (modules)
  trust: 0.9
- url: https://huggingface.co/speechbrain/emotion-recognition-wav2vec2-IEMOCAP/blob/main/custom_interface.py
  title: speechbrain/emotion-recognition-wav2vec2-IEMOCAP — custom_interface.py (classification steps)
  trust: 0.9
- url: https://huggingface.co/speechbrain/emotion-recognition-wav2vec2-IEMOCAP/blob/main/label_encoder.txt
  title: speechbrain/emotion-recognition-wav2vec2-IEMOCAP — label_encoder.txt (four labels)
  trust: 0.9
- url: https://huggingface.co/facebook/wav2vec2-base/blob/main/config.json
  title: facebook/wav2vec2-base — config.json (encoder sizes)
  trust: 0.9
---

## Summary

This model recognizes emotion in speech: given an audio recording, it assigns an emotion label, a task called speech emotion recognition. It is published by SpeechBrain, an open-source speech toolkit, as a fine-tuned wav2vec2 (base) model trained on the training data of the IEMOCAP dataset. The card marks the language as English.

## Intended Use

The card says the repository provides the tools needed to perform emotion recognition with the fine-tuned model through SpeechBrain, using a custom predictor class loaded with the foreign_class function.

## Running It

The card says the system was trained on recordings sampled at 16 kHz with a single channel, and that the classify_file function automatically resamples audio and selects one channel when needed. It asks users to install the development version of SpeechBrain.
