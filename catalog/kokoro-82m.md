---
id: model/kokoro-82m
type: model
slug: kokoro-82m
schema_version: 1.1
title: Kokoro-82M
organization: organization/hexgrad
release_date: '2024-12-26'
release_year: 2024
release_month: 12
license: license/apache-2-0
model_category: audio
tasks:
- task/text-to-speech
parameters_active_b: 0.082
base_models:
- name: yl4579/StyleTTS2-LJSpeech
  address: https://huggingface.co/yl4579/StyleTTS2-LJSpeech
summary: Kokoro is an open-weight text-to-speech (TTS) model, which turns written text into spoken audio, with 82 million parameters. The card lists its architecture as StyleTTS 2 with ISTFTNet, designed by Li et al., and gives yl4579/StyleTTS2-LJSpeech as the base model; the Kokoro weights themselves were trained by the model page's author. The v1.0 release is listed with 8 languages and 54 voices.
intended_use: The card says that because the weights are Apache-licensed, Kokoro can be deployed anywhere from production environments to personal projects, and it welcomes use in real applications.
what_sets_it_apart: The card claims comparable quality to larger models while being faster and cheaper to run. It describes the model as decoder-only, with no diffusion and no encoder released. Per the card, training used only permissive or non-copyrighted audio (public domain, Apache or MIT licensed, and synthetic audio from closed TTS models), a few hundred hours in total, at a stated cost of about 1000 A100 80GB GPU hours.
running_it: The card shows a Python example using the kokoro package (0.9.2 or later) with the soundfile library and espeak-ng installed. It picks a language with a language code and a voice name, and writes 24000 Hz audio. It notes that the package relies on the misaki library for turning text into phonemes.
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
tags:
- text-to-speech
updated: '2026-11-23'
sources:
- url: https://huggingface.co/hexgrad/Kokoro-82M
  title: hexgrad/Kokoro-82M — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2306.07691
  title: arXiv:2306.07691
  trust: 0.95
- url: https://arxiv.org/abs/2203.02395
  title: arXiv:2203.02395
  trust: 0.95
---

## Summary

Kokoro is an open-weight text-to-speech (TTS) model, which turns written text into spoken audio, with 82 million parameters. The card lists its architecture as StyleTTS 2 with ISTFTNet, designed by Li et al., and gives yl4579/StyleTTS2-LJSpeech as the base model; the Kokoro weights themselves were trained by the model page's author. The v1.0 release is listed with 8 languages and 54 voices.

## Intended Use

The card says that because the weights are Apache-licensed, Kokoro can be deployed anywhere from production environments to personal projects, and it welcomes use in real applications.

## What Sets It Apart

The card claims comparable quality to larger models while being faster and cheaper to run. It describes the model as decoder-only, with no diffusion and no encoder released. Per the card, training used only permissive or non-copyrighted audio (public domain, Apache or MIT licensed, and synthetic audio from closed TTS models), a few hundred hours in total, at a stated cost of about 1000 A100 80GB GPU hours.

## Running It

The card shows a Python example using the kokoro package (0.9.2 or later) with the soundfile library and espeak-ng installed. It picks a language with a language code and a voice name, and writes 24000 Hz audio. It notes that the package relies on the misaki library for turning text into phonemes.
