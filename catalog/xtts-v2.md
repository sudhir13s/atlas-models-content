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
---

## Summary

XTTS-v2 is a text-to-speech model from Coqui, which turns written text into spoken audio. The card says it can clone a voice from a short 6-second audio clip, speak in 17 languages, and output audio at a 24 kHz sampling rate.

## Intended Use

The card lists voice cloning, emotion and style transfer by cloning, cross-language voice cloning and multilingual speech generation. It notes that the model is under the Coqui Public Model License, and that the same or a similar model powers Coqui Studio and the Coqui API.

## What Sets It Apart

Compared with XTTS-v1, the card lists two new languages (Hungarian and Korean), architectural improvements for speaker conditioning, support for multiple speaker references and interpolation between speakers, stability improvements, and better prosody and audio quality.
