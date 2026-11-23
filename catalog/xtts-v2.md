---
id: model/xtts-v2
type: model
slug: xtts-v2
schema_version: 1.1
title: XTTS-v2
organization: organization/coqui
release_date: '2023-10-31'
release_year: 2023
release_month: 10
license_note: coqui-public-model-license
model_category: audio
tasks:
- task/text-to-speech
summary: XTTS-v2 is a text-to-speech model from Coqui, which turns written text into spoken audio. The card says it can clone a voice from a short 6-second audio clip, speak in 17 languages, and output audio at a 24 kHz sampling rate.
intended_use: The card lists voice cloning, emotion and style transfer by cloning, cross-language voice cloning and multilingual speech generation. It notes that the model is under the Coqui Public Model License, and that the same or a similar model powers Coqui Studio and the Coqui API.
what_sets_it_apart: Compared with XTTS-v1, the card lists two new languages (Hungarian and Korean), architectural improvements for speaker conditioning, support for multiple speaker references and interpolation between speakers, stability improvements, and better prosody and audio quality.
pipeline:
  source: https://github.com/coqui-ai/TTS/blob/dev/TTS/tts/models/xtts.py
  summary: A short recording of the target voice (left) is condensed into 32 vectors. A GPT-2 style transformer reads them with the text and writes audio codes one at a time. Its hidden states for those codes go to a HiFi-GAN decoder, which also reads a speaker embedding of the same recording, and out comes 24 kHz speech.
  side_into: join
  stages:
  - id: reference
    role: input
    title: Reference recording
    details:
    - up to 30 s · 6 s chunks
    side: true
  - id: mel
    role: step
    title: Mel spectrogram
    details:
    - 80 bins · 22,050 Hz
    side: true
  - id: conditioning-encoder
    role: audioEncoder
    title: Conditioning encoder
    details:
    - attention layers · width 1024
    note: Turns the spectrogram of each chunk into a sequence of vectors at the width of the transformer.
    side: true
  - id: perceiver
    role: projector
    title: Perceiver resampler
    details:
    - 32 learned queries · 2 layers
    note: Thirty-two learned vectors attend over the encoder output and keep a fixed-size summary of the voice. The summaries of the chunks are averaged.
    side: true
  - id: reference-again
    role: input
    title: The same recording
    side: true
    into: hifigan
  - id: speaker-encoder
    role: audioEncoder
    title: Speaker encoder
    details:
    - ResNet
    - one 512-number vector
    note: Turns the reference recording into one vector that conditions every upsampling layer of the decoder.
    side: true
  - id: text
    role: tokenizer
    title: Text tokens
    details:
    - BPE · language tag · 6,681 tokens
  - id: text-embedding
    role: embedding
    title: Token + position embedding
    details:
    - width 1024
  - id: join
    role: embedding
    title: Voice + text + codes
    details:
    - one sequence
    note: The 32 voice vectors come first, then the text embeddings, then the embeddings of the audio codes written so far.
  - id: gpt-block
    role: attention
    title: Attention + feed-forward
    details:
    - GPT-2 layer · 16 heads
    frame: Transformer block
    repeat: 30
  - id: final-norm
    role: normalisation
    title: Final LayerNorm
  - id: code-head
    role: outputHead
    title: Audio-code head
    details:
    - 1,026 codes · one per step
    note: Picks the next audio code, one at a time, until the stop code. The model then runs once more over all the codes, and its last hidden states, not the codes, go to the decoder.
  - id: hifigan
    role: feedForward
    title: HiFi-GAN decoder
    details:
    - reads a speaker embedding
    note: Upsamples the hidden states into a waveform, with the speaker embedding conditioning every upsampling layer.
  - id: audio
    role: outputHead
    title: Speech
    details:
    - 24 kHz
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-to-speech
  library: coqui
hub_signals:
  downloads_30d: 4979633
  likes: 3977
  last_modified: '2023-12-11'
  observed_at: '2026-11-21'
  source: https://huggingface.co/coqui/XTTS-v2
aliases:
- coqui/XTTS-v2
lineage:
  parents: []
  children: []
quotes:
  summary: ⓍTTS is a Voice generation model that lets you clone voices into different languages by using just a quick 6-second audio clip.
  intended_use: Emotion and style transfer by cloning.
  what_sets_it_apart: Enables the use of multiple speaker references and interpolation between speakers.
tags:
- text-to-speech
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/coqui/XTTS-v2
  title: coqui/XTTS-v2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/coqui/XTTS-v2/blob/main/config.json
  title: coqui/XTTS-v2 — config.json
  trust: 0.9
- url: https://github.com/coqui-ai/TTS/blob/dev/TTS/tts/models/xtts.py
  title: 'coqui-ai/TTS — TTS/tts/models/xtts.py: model code (GitHub)'
  trust: 0.9
- url: https://github.com/coqui-ai/TTS/blob/dev/TTS/tts/layers/xtts/gpt.py
  title: 'coqui-ai/TTS — TTS/tts/layers/xtts/gpt.py: conditioning and GPT layers (GitHub)'
  trust: 0.9
- url: https://github.com/coqui-ai/TTS/blob/dev/TTS/tts/layers/xtts/hifigan_decoder.py
  title: 'coqui-ai/TTS — TTS/tts/layers/xtts/hifigan_decoder.py: decoder and speaker encoder (GitHub)'
  trust: 0.9
---

## Summary

XTTS-v2 is a text-to-speech model from Coqui, which turns written text into spoken audio. The card says it can clone a voice from a short 6-second audio clip, speak in 17 languages, and output audio at a 24 kHz sampling rate.

## Intended Use

The card lists voice cloning, emotion and style transfer by cloning, cross-language voice cloning and multilingual speech generation. It notes that the model is under the Coqui Public Model License, and that the same or a similar model powers Coqui Studio and the Coqui API.

## What Sets It Apart

Compared with XTTS-v1, the card lists two new languages (Hungarian and Korean), architectural improvements for speaker conditioning, support for multiple speaker references and interpolation between speakers, stability improvements, and better prosody and audio quality.
