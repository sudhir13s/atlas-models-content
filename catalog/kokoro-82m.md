---
id: model/kokoro-82m
type: model
slug: kokoro-82m
schema_version: 3.0
title: Kokoro-82M
organization: organization/hexgrad
release_date: '2024-12-26'
license: license/apache-2-0
model_category: audio
model_kind: audio-speech
tasks:
- task/text-to-speech
parameters_active_b: 0.082
base_models:
- name: yl4579/StyleTTS2-LJSpeech
  address: https://huggingface.co/yl4579/StyleTTS2-LJSpeech
summary: Kokoro is an open-weight text-to-speech (TTS) model, which turns written text into spoken audio, with 82 million parameters. The card lists its architecture as StyleTTS 2 with ISTFTNet, designed by Li et al., and gives yl4579/StyleTTS2-LJSpeech as the base model; the Kokoro weights themselves were trained by the model page's author. The v1.0 release is listed with 8 languages and 54 voices; the voices page names American English, British English, Japanese, Mandarin Chinese, Spanish, French, Hindi, Italian and Brazilian Portuguese, and says support for languages other than English may be absent or thin. The card's metadata tags English only.
intended_use: The card says that because the weights are Apache-licensed, Kokoro can be deployed anywhere from production environments to personal projects, and it welcomes use in real applications.
what_sets_it_apart: The card claims comparable quality to larger models while being faster and cheaper to run. It describes the model as decoder-only, with no diffusion and no encoder released. Per the card, training used only permissive or non-copyrighted audio (public domain, Apache or MIT licensed, and synthetic audio from closed TTS models), a few hundred hours in total, at a stated cost of about 1000 A100 80GB GPU hours.
running_it: The card shows a Python example using the kokoro package (0.9.2 or later) with the soundfile library and espeak-ng installed. It picks a language with a language code and a voice name, and writes 24000 Hz audio. It notes that the package relies on the misaki library for turning text into phonemes.
pipeline:
  source: https://github.com/hexgrad/kokoro/blob/main/kokoro/model.py
  summary: The model reads phonemes, not letters, and makes the whole utterance in one pass, with no step-by-step loop. The main column predicts how long each phoneme lasts and the pitch and loudness over time; a second text encoder (left) gives the sound content. A chosen voice, a style vector of 256 numbers, steers both, and a decoder turns everything into a waveform.
  side_into: decoder
  stages:
  - id: side-phonemes
    role: input
    title: Phonemes
    side: true
  - id: phoneme-embedding
    role: embedding
    title: Phoneme embedding
    details:
    - 178 symbols · width 512
    side: true
  - id: text-convolutions
    role: textEncoder
    title: Convolution layers
    details:
    - 3 layers · kernel 5
    side: true
  - id: text-lstm
    role: textEncoder
    title: Bidirectional LSTM
    note: A recurrent layer that reads the phoneme vectors in both directions. Its output is stretched by the predicted durations before it reaches the decoder.
    side: true
  - id: phonemes
    role: input
    title: Phonemes
    details:
    - from text by the misaki library
  - id: plbert
    role: textEncoder
    title: PL-BERT (ALBERT)
    details:
    - 12 layers · 12 heads · width 768
    note: A BERT-style transformer, built as ALBERT, that reads the phonemes in context. A linear layer then maps its output to width 512.
  - id: duration-encoder
    role: feedForward
    title: Duration encoder
    details:
    - 3 bidirectional LSTMs · voice style
    note: Recurrent layers read the phoneme vectors together with the last 128 numbers of the voice style vector.
  - id: duration-head
    role: outputHead
    title: Duration per phoneme
    details:
    - in audio frames · divided by speed
    note: Predicts how many frames each phoneme lasts; the speed setting divides the result.
  - id: align
    role: step
    title: Repeat by duration
    note: Each phoneme vector is repeated as many times as its predicted duration, so the sequence now runs in time frames, not phonemes.
  - id: prosody
    role: feedForward
    title: Pitch + noise predictor
    details:
    - F0 curve and noise curve
    note: Predicts the pitch (F0) and a noise curve for every frame from the stretched vectors and the voice style.
  - id: decoder
    role: feedForward
    title: Decoder
    details:
    - AdaIN residual blocks · voice style
    note: Combines the sound content, the pitch and the noise curve. Adaptive instance normalisation (AdaIN) lets the first 128 numbers of the voice style vector steer each block.
  - id: generator
    role: outputHead
    title: iSTFTNet generator
    details:
    - upsample ×10, ×6 · inverse STFT
    note: Upsampling convolutions raise the time resolution, then an inverse short-time Fourier transform (STFT) turns the result into samples.
  - id: audio
    role: outputHead
    title: Audio
    details:
    - 24,000 samples per second
filters:
  size_category: tiny
  is_gated: false
  pipeline_tag: text-to-speech
hub_signals:
  downloads_30d: 13254461
  likes: 7486
  last_modified: '2025-04-10'
  observed_at: '2026-11-21'
  source: https://huggingface.co/hexgrad/Kokoro-82M
aliases:
- hexgrad/Kokoro-82M
- Kokoro
quotes:
  summary: is an open-weight TTS model with 82 million parameters.
  intended_use: With Apache-licensed weights, Kokoro can be deployed anywhere from production environments to personal projects.
  what_sets_it_apart: Kokoro was trained exclusively on **permissive/non-copyrighted audio data** and IPA phoneme labels.
  running_it: pipeline = KPipeline(lang_code='a')
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/hexgrad/Kokoro-82M
  title: hexgrad/Kokoro-82M — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/hexgrad/Kokoro-82M/blob/main/VOICES.md
  title: hexgrad/Kokoro-82M — VOICES.md (languages and voices)
  trust: 0.9
- url: https://arxiv.org/abs/2306.07691
  title: arXiv:2306.07691
  trust: 0.95
- url: https://arxiv.org/abs/2203.02395
  title: arXiv:2203.02395
  trust: 0.95
- url: https://github.com/hexgrad/kokoro/blob/main/kokoro/model.py
  title: 'hexgrad/kokoro — kokoro/model.py: model code (GitHub)'
  trust: 0.9
- url: https://github.com/hexgrad/kokoro/blob/main/kokoro/modules.py
  title: 'hexgrad/kokoro — kokoro/modules.py: text encoder and prosody predictor (GitHub)'
  trust: 0.9
- url: https://github.com/hexgrad/kokoro/blob/main/kokoro/istftnet.py
  title: 'hexgrad/kokoro — kokoro/istftnet.py: decoder and generator (GitHub)'
  trust: 0.9
- url: https://huggingface.co/hexgrad/Kokoro-82M/blob/main/config.json
  title: hexgrad/Kokoro-82M — config.json
  trust: 0.9
---


