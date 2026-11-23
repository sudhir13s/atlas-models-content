---
id: model/flux-2-dev
type: model
slug: flux-2-dev
schema_version: 1.1
title: FLUX.2-dev
organization: organization/black-forest-labs
release_date: '2025-11-22'
release_year: 2025
release_month: 11
license_note: flux-non-commercial-license
model_category: generation
tasks:
- task/image-editing
total_parameters: 32223281152
summary: FLUX.2 [dev] is an image model from Black Forest Labs that makes and edits pictures from written instructions. The card describes it as a 32 billion parameter rectified flow transformer that generates, edits and combines images based on text instructions. Its weights are open, but access on Hugging Face requires accepting the FLUX Non-Commercial License Agreement.
intended_use: The card lists open text-to-image generation, single-reference editing and multi-reference editing. It says the open weights are meant to drive new scientific research and to help artists build new workflows, and that generated outputs can be used for personal, scientific and commercial purposes as described in the license. For local use on a consumer graphics card such as an RTX 4090, the card points to a 4-bit quantized version in the diffusers library.
what_sets_it_apart: The card says one model can use a character, object or style reference without any fine-tuning, so no extra training is needed per subject. It also says the model was trained using guidance distillation, a training technique, which the card says makes it more efficient.
filters:
  size_category: large
  is_gated: true
  pipeline_tag: image-to-image
  library: diffusers
hub_signals:
  downloads_30d: 287646
  likes: 2606
  last_modified: '2026-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/black-forest-labs/FLUX.2-dev
aliases:
- black-forest-labs/FLUX.2-dev
lineage:
  parents: []
  children: []
quotes:
  summary: a 32 billion parameter rectified flow transformer capable of generating, editing and combining images based on text instructions
  intended_use: Open weights to drive new scientific research, and empower artists to develop innovative workflows.
  what_sets_it_apart: Trained using guidance distillation, making FLUX.2 [dev] more efficient.
tags:
- image-to-image
updated: '2026-11-23'
sources:
- url: https://huggingface.co/black-forest-labs/FLUX.2-dev
  title: black-forest-labs/FLUX.2-dev — model page (Hugging Face)
  trust: 0.9
---

## Summary

FLUX.2 [dev] is an image model from Black Forest Labs that makes and edits pictures from written instructions. The card describes it as a 32 billion parameter rectified flow transformer that generates, edits and combines images based on text instructions. Its weights are open, but access on Hugging Face requires accepting the FLUX Non-Commercial License Agreement.

## Intended Use

The card lists open text-to-image generation, single-reference editing and multi-reference editing. It says the open weights are meant to drive new scientific research and to help artists build new workflows, and that generated outputs can be used for personal, scientific and commercial purposes as described in the license. For local use on a consumer graphics card such as an RTX 4090, the card points to a 4-bit quantized version in the diffusers library.

## What Sets It Apart

The card says one model can use a character, object or style reference without any fine-tuning, so no extra training is needed per subject. It also says the model was trained using guidance distillation, a training technique, which the card says makes it more efficient.
