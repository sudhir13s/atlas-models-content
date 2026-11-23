---
id: model/flux-1-kontext-dev
type: model
slug: flux-1-kontext-dev
schema_version: 1.1
title: FLUX.1-Kontext-dev
organization: organization/black-forest-labs
release_date: '2025-05-28'
release_year: 2025
release_month: 5
license_note: flux-1-dev-non-commercial-license
model_category: generation
tasks:
- task/image-editing
total_parameters: 11901408320
summary: FLUX.1 Kontext [dev] is an image-editing model published by Black Forest Labs. You give it an existing image and a text instruction, and it returns the edited image. The card describes it as a 12 billion parameter rectified flow transformer, a type of image-generation network, released with open weights.
what_sets_it_apart: 'The card lists these features: it changes an existing image from an edit instruction, it keeps a character, style or object as a reference without any fine-tuning, and it stays consistent over several successive edits with minimal visual drift. It says the model was trained with guidance distillation, which makes it more efficient.'
running_it: The card says the repository includes filters for illegal or infringing content, and that filters or manual review must be used with the model under the terms of the FLUX.1 [dev] Non-Commercial License. It points to a reference implementation with sampling code on GitHub and to use through the diffusers library.
filters:
  size_category: medium
  is_gated: true
  pipeline_tag: image-to-image
  library: diffusers
hub_signals:
  downloads_30d: 165515
  likes: 2976
  last_modified: '2026-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/black-forest-labs/FLUX.1-Kontext-dev
aliases:
- black-forest-labs/FLUX.1-Kontext-dev
lineage:
  parents: []
  children: []
quotes:
  summary: is a 12 billion parameter rectified flow transformer capable of editing images based on text instructions.
  what_sets_it_apart: Robust consistency allows users to refine an image through multiple successive edits with minimal visual drift.
  running_it: Filters or manual review must be used with the model under the terms of the FLUX.1 [dev] Non-Commercial License.
tags:
- image-to-image
updated: '2026-11-23'
sources:
- url: https://huggingface.co/black-forest-labs/FLUX.1-Kontext-dev
  title: black-forest-labs/FLUX.1-Kontext-dev — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2506.15742
  title: arXiv:2506.15742
  trust: 0.95
---

## Summary

FLUX.1 Kontext [dev] is an image-editing model published by Black Forest Labs. You give it an existing image and a text instruction, and it returns the edited image. The card describes it as a 12 billion parameter rectified flow transformer, a type of image-generation network, released with open weights.

## What Sets It Apart

The card lists these features: it changes an existing image from an edit instruction, it keeps a character, style or object as a reference without any fine-tuning, and it stays consistent over several successive edits with minimal visual drift. It says the model was trained with guidance distillation, which makes it more efficient.

## Running It

The card says the repository includes filters for illegal or infringing content, and that filters or manual review must be used with the model under the terms of the FLUX.1 [dev] Non-Commercial License. It points to a reference implementation with sampling code on GitHub and to use through the diffusers library.
