---
id: model/re-use
type: model
slug: re-use
schema_version: 1.1
title: RE-USE
organization: organization/nvidia
release_date: '2026-03-17'
release_year: 2026
release_month: 3
license_note: other
model_category: audio
tasks:
- task/audio-enhancement
parameters_active_b: 0.01
total_parameters: 9607747
summary: 'RE-USE is a speech enhancement model from NVIDIA: it takes degraded speech, for example with noise or echo, and returns a cleaner version. The card calls it multilingual universal speech enhancement, meaning one model for many kinds of degradation, input sampling rates and languages. The card says it is for research and development only, and points to NVIDIA-NemotronLabs-RE-USE for the commercially friendly model.'
intended_use: The card says the goal is to restore the quality of diverse degraded speech while preserving fidelity, which it spells out as keeping the linguistic content, speaker identity, emotion and accent unchanged. The model is for research and development only.
what_sets_it_apart: The card says one model handles diverse degradations, including noise, reverberation, clipping, bandwidth limits, codec artifacts, packet loss and low-quality microphones. It supports input sampling rates from 8 to 48 kHz and works across languages, and the authors say it balances quality and fidelity in line with the distortion-perception trade-off theory.
running_it: The card gives an inference.sh script for short files and an inference_chunk.sh script for long files that could run the GPU out of memory, with chunk_size_in_seconds and hop_length_portion set in the script. Bandwidth extension is enabled through the BWE argument and needs the resampy package.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: audio-to-audio
  library: mamba-ssm
hub_signals:
  downloads_30d: 10735
  likes: 158
  last_modified: '2026-10-23'
  observed_at: '2026-11-21'
  source: https://huggingface.co/nvidia/RE-USE
aliases:
- nvidia/RE-USE
lineage:
  parents: []
  children: []
quotes:
  summary: the goal is to restore the **quality** of diverse degraded speech
  intended_use: This model is for research and development only.
  what_sets_it_apart: our proposed single model achieves a good balance between these two objectives
  running_it: You can also configure the `chunk_size_in_seconds` and `hop_length_portion` directly in the script.
tags:
- audio-to-audio
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/RE-USE
  title: nvidia/RE-USE — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/RE-USE/blob/main/config.json
  title: nvidia/RE-USE — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2603.02641
  title: arXiv:2603.02641
  trust: 0.95
---

## Summary

RE-USE is a speech enhancement model from NVIDIA: it takes degraded speech, for example with noise or echo, and returns a cleaner version. The card calls it multilingual universal speech enhancement, meaning one model for many kinds of degradation, input sampling rates and languages. The card says it is for research and development only, and points to NVIDIA-NemotronLabs-RE-USE for the commercially friendly model.

## Intended Use

The card says the goal is to restore the quality of diverse degraded speech while preserving fidelity, which it spells out as keeping the linguistic content, speaker identity, emotion and accent unchanged. The model is for research and development only.

## What Sets It Apart

The card says one model handles diverse degradations, including noise, reverberation, clipping, bandwidth limits, codec artifacts, packet loss and low-quality microphones. It supports input sampling rates from 8 to 48 kHz and works across languages, and the authors say it balances quality and fidelity in line with the distortion-perception trade-off theory.

## Running It

The card gives an inference.sh script for short files and an inference_chunk.sh script for long files that could run the GPU out of memory, with chunk_size_in_seconds and hop_length_portion set in the script. Bandwidth extension is enabled through the BWE argument and needs the resampy package.
