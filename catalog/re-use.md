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
pipeline:
  source: https://huggingface.co/nvidia/RE-USE/blob/main/models/generator_SEMamba_time_d4.py
  summary: The degraded speech is turned into a spectrogram, a picture of its frequencies over time, split into magnitude and phase. A convolutional encoder, 30 blocks of bidirectional Mamba layers that scan along time and then along frequency, and two convolutional decoders predict a clean magnitude and phase, and an inverse transform turns them back into a waveform.
  stages:
  - id: audio-in
    role: input
    title: Degraded speech
    details:
    - mono · 8 to 48 kHz
  - id: resample
    role: step
    title: Resample (optional)
    details:
    - for bandwidth extension
    note: For bandwidth extension (BWE) the script first resamples the audio to the target rate; the model then fills in the missing high frequencies.
  - id: stft
    role: step
    title: STFT magnitude + phase
    details:
    - window 40 ms · hop 5 ms
    note: A short-time Fourier transform (STFT) splits the sound into frequencies over time. The window and hop are 320 and 40 samples at 8 kHz and scale with the sample rate, and the magnitude is compressed with log(1 + x).
  - id: encoder
    role: audioEncoder
    title: Dense encoder
    details:
    - convolutions · 2 → 64 channels
    note: Densely connected convolution layers read the magnitude and phase as two channels and give 64 feature channels. A strided convolution then makes the grid four times shorter in time and two times narrower in frequency.
  - id: time-mamba
    role: attention
    title: Mamba along time
    details:
    - forward + backward scan
    note: A Mamba layer, a state-space sequence model, reads each frequency band through time in both directions, and the two results are joined by a linear layer.
    frame: TF-Mamba block
    repeat: 30
  - id: frequency-mamba
    role: attention
    title: Mamba along frequency
    details:
    - forward + backward scan
    note: The same kind of layer reads each time frame across the frequencies, again in both directions.
    frame: TF-Mamba block
  - id: decoders
    role: outputHead
    title: Magnitude + phase decoders
    details:
    - two convolutional decoders
    note: Both decoders bring the grid back to full size. One predicts the clean magnitude and the other, in parallel, the clean phase.
  - id: istft
    role: step
    title: Inverse STFT
    note: Combines the magnitude and phase and turns the spectrogram back into samples, at the same rate as the input.
  - id: audio-out
    role: outputHead
    title: Enhanced speech
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
- url: https://huggingface.co/nvidia/RE-USE/blob/main/models/generator_SEMamba_time_d4.py
  title: nvidia/RE-USE — models/generator_SEMamba_time_d4.py (model code)
  trust: 0.9
- url: https://huggingface.co/nvidia/RE-USE/blob/main/models/mamba_block2_SEMamba.py
  title: nvidia/RE-USE — models/mamba_block2_SEMamba.py (time-frequency Mamba block)
  trust: 0.9
- url: https://huggingface.co/nvidia/RE-USE/blob/main/inference.py
  title: nvidia/RE-USE — inference.py (resampling, STFT and inverse STFT)
  trust: 0.9
---

## Summary

RE-USE is a speech enhancement model from NVIDIA: it takes degraded speech, for example with noise or echo, and returns a cleaner version. The card calls it multilingual universal speech enhancement, meaning one model for many kinds of degradation, input sampling rates and languages. The card says it is for research and development only, and points to NVIDIA-NemotronLabs-RE-USE for the commercially friendly model.

## Intended Use

The card says the goal is to restore the quality of diverse degraded speech while preserving fidelity, which it spells out as keeping the linguistic content, speaker identity, emotion and accent unchanged. The model is for research and development only.

## What Sets It Apart

The card says one model handles diverse degradations, including noise, reverberation, clipping, bandwidth limits, codec artifacts, packet loss and low-quality microphones. It supports input sampling rates from 8 to 48 kHz and works across languages, and the authors say it balances quality and fidelity in line with the distortion-perception trade-off theory.

## Running It

The card gives an inference.sh script for short files and an inference_chunk.sh script for long files that could run the GPU out of memory, with chunk_size_in_seconds and hop_length_portion set in the script. Bandwidth extension is enabled through the BWE argument and needs the resampy package.
