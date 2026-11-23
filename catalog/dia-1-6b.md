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
