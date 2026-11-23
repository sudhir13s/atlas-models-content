---
id: model/dia-1-6b
type: model
slug: dia-1-6b
schema_version: 1.1
title: Dia-1.6B
organization: organization/nari-labs
release_date: '2025-04-20'
release_year: 2025
release_month: 4
license: license/apache-2-0
model_category: audio
tasks:
- task/text-to-speech
parameters_active_b: 1.61
total_parameters: 1611160576
summary: Dia is a text-to-speech model, which turns written text into spoken audio, created by Nari Labs. The card gives it 1.6B parameters and says it generates dialogue directly from a transcript in which the speakers are marked with [S1] and [S2] tags. It supports English only.
intended_use: The card's disclaimer says it is intended for research and educational use. It forbids producing audio that resembles real people without permission, generating misleading content such as fake news, and any illegal or malicious use.
what_sets_it_apart: The card says the output can be conditioned on audio to control emotion and tone, and that the model can produce nonverbal sounds such as laughter, coughing and throat clearing, written as tags like (laughs). It lists voice cloning as a feature. The model was not fine-tuned on a specific voice, so each run gives different voices unless an audio prompt is added or the random seed is fixed.
running_it: The card says it has been tested on GPUs only (PyTorch 2.0 or later, CUDA 12.6), with CPU support still to come. The full version needs around 10GB of video memory (VRAM). On an A4000 GPU it generates roughly 40 tokens per second, where 86 tokens equal one second of audio, and the first run also downloads the Descript Audio Codec.
limitations: The card says the model only supports English generation at the moment. Several nonverbal tags beyond the main ones are recognized but might give unexpected output.
pipeline:
  source: https://github.com/nari-labs/dia/blob/main/dia/layers.py
  summary: An encoder reads the transcript (left) once. A decoder then writes the audio as codes of the Descript Audio Codec (DAC), nine codes per time step, reading the transcript through cross-attention at every step. The DAC decoder turns the finished codes into a waveform.
  side_into: cross-attention
  stages:
  - id: transcript
    role: input
    title: Transcript
    details:
    - speakers tagged [S1] and [S2]
    side: true
  - id: byte-embedding
    role: embedding
    title: Byte embedding
    details:
    - 256 byte values · width 1024
    note: The text is turned into its UTF-8 bytes, with [S1] and [S2] replaced by the bytes 1 and 2, and each byte gets a learned vector.
    side: true
  - id: encoder-block
    role: textEncoder
    title: Attention + feed-forward
    details:
    - 16 heads · RMSNorm before each part
    frame: Encoder block
    repeat: 12
    side: true
  - id: encoder-norm
    role: normalisation
    title: Final RMSNorm
    side: true
  - id: codes
    role: input
    title: Audio codes so far
    details:
    - 9 codebooks · delay pattern
    note: Each time step holds one code from each of nine DAC codebooks. The codebooks are shifted in time by a fixed delay pattern (0, 8, 9 … 15 steps), and an audio prompt, when given, is encoded into codes and placed first.
  - id: code-embedding
    role: embedding
    title: Sum of code embeddings
    details:
    - one table per codebook · width 2048
    note: Each of the nine codes is looked up in its own embedding table and the nine vectors are added into one.
  - id: self-attention
    role: attention
    title: Self-attention
    details:
    - 16 heads · 4 KV heads · RoPE
    frame: Decoder block
    repeat: 18
  - id: cross-attention
    role: attention
    title: Cross-attention
    details:
    - reads the encoded transcript
    frame: Decoder block
  - id: feed-forward
    role: feedForward
    title: Feed-forward
    details:
    - width 8192
    frame: Decoder block
  - id: decoder-norm
    role: normalisation
    title: Final RMSNorm
  - id: output
    role: outputHead
    title: Output head
    details:
    - 9 codebooks × 1028 logits
    note: Gives the next code for each of the nine codebooks. Generation runs one time step at a time; by default the pipeline mixes predictions with and without the text (classifier-free guidance, scale 3.0).
  - id: dac-decoder
    role: autoencoder
    title: DAC audio decoder
    details:
    - codes to waveform
    note: After the delay pattern is undone, the Descript Audio Codec turns the codes back into a sound wave.
  - id: audio
    role: outputHead
    title: Audio
filters:
  size_category: small
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-to-speech
hub_signals:
  downloads_30d: 4835
  likes: 2940
  last_modified: '2025-06-01'
  observed_at: '2026-11-21'
  source: https://huggingface.co/nari-labs/Dia-1.6B
aliases:
- nari-labs/Dia-1.6B
lineage:
  parents: []
  children: []
quotes:
  summary: Dia **directly generates highly realistic dialogue from a transcript**
  intended_use: intended for research and educational use
  what_sets_it_apart: The model can also produce nonverbal communications like laughter, coughing, clearing throat, etc.
  running_it: The full version of Dia requires around 10GB of VRAM to run.
  limitations: The model only supports English generation at the moment.
tags:
- text-to-speech
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nari-labs/Dia-1.6B
  title: nari-labs/Dia-1.6B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nari-labs/Dia-1.6B/blob/main/config.json
  title: nari-labs/Dia-1.6B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2305.09636
  title: arXiv:2305.09636
  trust: 0.95
- url: https://github.com/nari-labs/dia/blob/main/dia/layers.py
  title: 'nari-labs/dia — dia/layers.py: model code (GitHub)'
  trust: 0.9
- url: https://github.com/nari-labs/dia/blob/main/dia/model.py
  title: 'nari-labs/dia — dia/model.py: generation and DAC decoding (GitHub)'
  trust: 0.9
---

## Summary

Dia is a text-to-speech model, which turns written text into spoken audio, created by Nari Labs. The card gives it 1.6B parameters and says it generates dialogue directly from a transcript in which the speakers are marked with [S1] and [S2] tags. It supports English only.

## Intended Use

The card's disclaimer says it is intended for research and educational use. It forbids producing audio that resembles real people without permission, generating misleading content such as fake news, and any illegal or malicious use.

## What Sets It Apart

The card says the output can be conditioned on audio to control emotion and tone, and that the model can produce nonverbal sounds such as laughter, coughing and throat clearing, written as tags like (laughs). It lists voice cloning as a feature. The model was not fine-tuned on a specific voice, so each run gives different voices unless an audio prompt is added or the random seed is fixed.

## Running It

The card says it has been tested on GPUs only (PyTorch 2.0 or later, CUDA 12.6), with CPU support still to come. The full version needs around 10GB of video memory (VRAM). On an A4000 GPU it generates roughly 40 tokens per second, where 86 tokens equal one second of audio, and the first run also downloads the Descript Audio Codec.

## Limitations

The card says the model only supports English generation at the moment. Several nonverbal tags beyond the main ones are recognized but might give unexpected output.
