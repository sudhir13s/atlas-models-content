---
id: model/ast-finetuned-audioset-10-10-0-4593
type: model
slug: ast-finetuned-audioset-10-10-0-4593
schema_version: 3.0
title: ast-finetuned-audioset-10-10-0.4593
organization: organization/mit
release_date: '2022-11-14'
license_note: bsd-3-clause
model_category: audio
model_kind: audio-speech
tasks:
- task/audio-classification
parameters_active_b: 0.09
total_parameters: 86594063
summary: 'The Audio Spectrogram Transformer (AST) fine-tuned on AudioSet is an audio classification model: it takes a sound clip and assigns it to one of the sound categories of AudioSet, a large labelled collection of audio clips. It comes from the AST paper by Gong et al., and the Hugging Face team wrote its model card because the releasing team did not. It works by turning the audio into an image called a spectrogram and then reading that image with a Vision Transformer.'
intended_use: The card says the raw model can be used for classifying audio into one of the AudioSet classes, and points to the Transformers documentation for an example.
what_sets_it_apart: The card describes AST as the same design as ViT, the Vision Transformer, applied to audio instead of photographs. Audio is first converted into a spectrogram, a picture of how the sound energy spreads over frequency and time, and the transformer then processes that picture.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: pre
  stack: encoder
  input_stem: spectrogram-patches
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: audio-classification
  library: transformers
hub_signals:
  downloads_30d: 1940674
  likes: 392
  last_modified: '2023-09-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/MIT/ast-finetuned-audioset-10-10-0.4593
aliases:
- MIT/ast-finetuned-audioset-10-10-0.4593
lineage:
  parents: []
  children: []
quotes:
  summary: Audio Spectrogram Transformer (AST) model fine-tuned on AudioSet.
  intended_use: You can use the raw model for classifying audio into one of the AudioSet classes.
  what_sets_it_apart: Audio is first turned into an image (as a spectrogram), after which a Vision Transformer is applied.
tags:
- audio-spectrogram-transformer
updated: '2026-11-23'
sources:
- url: https://huggingface.co/MIT/ast-finetuned-audioset-10-10-0.4593
  title: MIT/ast-finetuned-audioset-10-10-0.4593 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/MIT/ast-finetuned-audioset-10-10-0.4593/blob/main/config.json
  title: MIT/ast-finetuned-audioset-10-10-0.4593 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2104.01778
  title: arXiv:2104.01778
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/audio_spectrogram_transformer/modeling_audio_spectrogram_transformer.py
  title: 'transformers/models/audio_spectrogram_transformer/modeling_audio_spectrogram_transformer.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


