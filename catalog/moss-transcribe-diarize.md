---
id: model/moss-transcribe-diarize
type: model
slug: moss-transcribe-diarize
schema_version: 1.1
title: MOSS-Transcribe-Diarize
organization: organization/openmoss-team
release_date: '2026-05-19'
release_year: 2026
release_month: 5
license: license/apache-2-0
model_category: audio
tasks:
- task/speech-language-models
parameters_active_b: 0.91
total_parameters: 908513280
context_window_tokens: 131072
summary: MOSS-Transcribe-Diarize 0.9B is a speech model from OpenMOSS-Team that turns a long recording of several people talking into a written transcript with timestamps and anonymous speaker labels such as [S01] and [S02]. Writing down the speech is transcription, and labeling who spoke when is called diarization. The card states support for more than 50 languages and single-pass processing of recordings up to 90 minutes long.
intended_use: The card says the model is built for meetings, calls, podcasts, interviews, lectures, videos and other long or messy multi-speaker recordings. Given an audio or video file it returns a speaker-aware transcript in one pass, can add acoustic event annotations, and accepts custom hotwords, meaning domain-specific terms to spell correctly.
what_sets_it_apart: 'The card says one model does transcription and diarization together instead of combining separate speech recognition (ASR) and diarization systems, so it produces time-aligned text with consistent speaker labels without a separate diarization pipeline. It describes the output as promptable: users can give custom transcription instructions.'
running_it: The card says the repository includes custom Transformers code, so loading the model requires trust_remote_code=True.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 16
  kv_heads: 8
  head_size: 128
  hidden_size: 1024
  intermediate_size: 3072
  context_window_tokens: 131072
  vocabulary_size: 151936
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: GQA
  is_gated: false
  pipeline_tag: audio-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 148877
  likes: 529
  last_modified: '2026-10-31'
  observed_at: '2026-11-21'
  source: https://huggingface.co/OpenMOSS-Team/MOSS-Transcribe-Diarize
aliases:
- OpenMOSS-Team/MOSS-Transcribe-Diarize
lineage:
  parents: []
  children: []
quotes:
  summary: an end-to-end audio understanding model for long-form multi-speaker transcription, diarization, timestamps, and acoustic event awareness
  intended_use: The model is built for meetings, calls, podcasts, interviews, lectures, videos, and other long or messy multi-speaker recordings.
  what_sets_it_apart: Instead of stitching together separate ASR and diarization systems
  running_it: includes the custom Transformers remote code required to load the model with `trust_remote_code=True`
tags:
- audio-text-to-text
- moss-transcribe-diarize
- dense
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/OpenMOSS-Team/MOSS-Transcribe-Diarize
  title: OpenMOSS-Team/MOSS-Transcribe-Diarize — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/OpenMOSS-Team/MOSS-Transcribe-Diarize/blob/main/config.json
  title: OpenMOSS-Team/MOSS-Transcribe-Diarize — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2601.01554
  title: arXiv:2601.01554
  trust: 0.95
---

## Summary

MOSS-Transcribe-Diarize 0.9B is a speech model from OpenMOSS-Team that turns a long recording of several people talking into a written transcript with timestamps and anonymous speaker labels such as [S01] and [S02]. Writing down the speech is transcription, and labeling who spoke when is called diarization. The card states support for more than 50 languages and single-pass processing of recordings up to 90 minutes long.

## Intended Use

The card says the model is built for meetings, calls, podcasts, interviews, lectures, videos and other long or messy multi-speaker recordings. Given an audio or video file it returns a speaker-aware transcript in one pass, can add acoustic event annotations, and accepts custom hotwords, meaning domain-specific terms to spell correctly.

## What Sets It Apart

The card says one model does transcription and diarization together instead of combining separate speech recognition (ASR) and diarization systems, so it produces time-aligned text with consistent speaker labels without a separate diarization pipeline. It describes the output as promptable: users can give custom transcription instructions.

## Running It

The card says the repository includes custom Transformers code, so loading the model requires trust_remote_code=True.
