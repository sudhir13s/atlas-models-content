---
id: model/nvidia-nemotronlabs-re-use
type: model
slug: nvidia-nemotronlabs-re-use
schema_version: 1.1
title: NVIDIA-NemotronLabs-RE-USE
organization: organization/nvidia
release_date: '2026-07-22'
release_year: 2026
release_month: 7
license_note: openmdw-1.1
model_category: audio
tasks:
- task/audio-enhancement
parameters_active_b: 0.01
total_parameters: 9607747
summary: NVIDIA NemotronLabs RE-USE is a speech enhancement model from NVIDIA. It takes noisy or degraded speech recordings and restores their quality, and the card says it is language-agnostic. The card describes it as a 9.6-million-parameter multilingual universal speech enhancement model that accepts input sampling rates from 8 to 48 kHz.
intended_use: 'The card lists degradations it is built to handle: additive noise, reverberation, clipping, bandwidth limitation, codec artifacts, packet loss and low-quality microphones. For overlapping speech it is trained to keep the main speaker and suppress the others. For better laughter preservation the card points to a separate RE-USE-laughter model.'
what_sets_it_apart: The card describes a 9.6-million-parameter model that works across many input sampling rates (8, 16, 22.05, 24, 32, 44.1 and 48 kHz) and across languages, with optional bandwidth extension. It says the model was trained on clips shorter than 10 seconds.
running_it: The card says to place noisy files in a folder and run the provided inference script. For files longer than about 30 seconds, or when the GPU runs out of memory, it provides a separate chunked inference script.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: audio-to-audio
  library: mamba-ssm
hub_signals:
  downloads_30d: 55397
  likes: 249
  last_modified: '2026-10-22'
  observed_at: '2026-11-21'
  source: https://huggingface.co/nvidia/NVIDIA-NemotronLabs-RE-USE
aliases:
- nvidia/NVIDIA-NemotronLabs-RE-USE
lineage:
  parents: []
  children: []
quotes:
  summary: NVIDIA NemotronLabs RE-USE is a 9.6-million-parameter
  intended_use: For overlapping speech, this model is trained to preserve the main speaker while suppressing other speakers.
  what_sets_it_apart: Support for **multiple input sampling rates**, including 8, 16, 22.05, 24, 32, 44.1, and 48 kHz.
  running_it: 'or cause GPU out-of-memory (OOM) errors, please use the following procedure instead:'
tags:
- audio-to-audio
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/NVIDIA-NemotronLabs-RE-USE
  title: nvidia/NVIDIA-NemotronLabs-RE-USE — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-NemotronLabs-RE-USE/blob/main/config.json
  title: nvidia/NVIDIA-NemotronLabs-RE-USE — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2603.02641
  title: arXiv:2603.02641
  trust: 0.95
- url: https://arxiv.org/abs/2606.25621
  title: arXiv:2606.25621
  trust: 0.95
---

## Summary

NVIDIA NemotronLabs RE-USE is a speech enhancement model from NVIDIA. It takes noisy or degraded speech recordings and restores their quality, and the card says it is language-agnostic. The card describes it as a 9.6-million-parameter multilingual universal speech enhancement model that accepts input sampling rates from 8 to 48 kHz.

## Intended Use

The card lists degradations it is built to handle: additive noise, reverberation, clipping, bandwidth limitation, codec artifacts, packet loss and low-quality microphones. For overlapping speech it is trained to keep the main speaker and suppress the others. For better laughter preservation the card points to a separate RE-USE-laughter model.

## What Sets It Apart

The card describes a 9.6-million-parameter model that works across many input sampling rates (8, 16, 22.05, 24, 32, 44.1 and 48 kHz) and across languages, with optional bandwidth extension. It says the model was trained on clips shorter than 10 seconds.

## Running It

The card says to place noisy files in a folder and run the provided inference script. For files longer than about 30 seconds, or when the GPU runs out of memory, it provides a separate chunked inference script.
