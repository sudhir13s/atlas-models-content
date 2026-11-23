---
id: model/nvidia-nemotronlabs-re-use
type: model
slug: nvidia-nemotronlabs-re-use
schema_version: 3.0
title: NVIDIA-NemotronLabs-RE-USE
organization: organization/nvidia
release_date: '2026-07-22'
license_note: openmdw-1.1
model_category: audio
model_kind: audio-speech
tasks:
- task/audio-enhancement
parameters_active_b: 0.01
total_parameters: 9607747
summary: NVIDIA NemotronLabs RE-USE is a speech enhancement model from NVIDIA. It takes noisy or degraded speech recordings and restores their quality, and the card says it is language-agnostic. The card describes it as a 9.6-million-parameter multilingual universal speech enhancement model that accepts input sampling rates from 8 to 48 kHz.
intended_use: 'The card lists degradations it is built to handle: additive noise, reverberation, clipping, bandwidth limitation, codec artifacts, packet loss and low-quality microphones. For overlapping speech it is trained to keep the main speaker and suppress the others. For better laughter preservation the card points to a separate RE-USE-laughter model.'
what_sets_it_apart: The card describes a 9.6-million-parameter model that works across many input sampling rates (8, 16, 22.05, 24, 32, 44.1 and 48 kHz) and across languages, with optional bandwidth extension. It says the model was trained on clips shorter than 10 seconds.
running_it: The card says to place noisy files in a folder and run the provided inference script. For files longer than about 30 seconds, or when the GPU runs out of memory, it provides a separate chunked inference script.
pipeline:
  source: https://huggingface.co/nvidia/NVIDIA-NemotronLabs-RE-USE/blob/main/models/generator_SEMamba_time_d4.py
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
tags: []
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
- url: https://huggingface.co/nvidia/NVIDIA-NemotronLabs-RE-USE/blob/main/models/generator_SEMamba_time_d4.py
  title: nvidia/NVIDIA-NemotronLabs-RE-USE — models/generator_SEMamba_time_d4.py (model code)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-NemotronLabs-RE-USE/blob/main/models/mamba_block2_SEMamba.py
  title: nvidia/NVIDIA-NemotronLabs-RE-USE — models/mamba_block2_SEMamba.py (time-frequency Mamba block)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-NemotronLabs-RE-USE/blob/main/inference.py
  title: nvidia/NVIDIA-NemotronLabs-RE-USE — inference.py (resampling, STFT and inverse STFT)
  trust: 0.9
---


