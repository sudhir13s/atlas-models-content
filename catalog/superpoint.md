---
id: model/superpoint
type: model
slug: superpoint
schema_version: 1.1
title: superpoint
organization: organization/magic-leap-community
release_date: '2024-03-13'
release_year: 2024
release_month: 3
license_note: other
model_category: vision
tasks:
- task/keypoint-detection
parameters_active_b: 0.0
total_parameters: 1300865
summary: SuperPoint is a model that finds interest points in an image, meaning distinctive spots such as corners, and produces a descriptor for each one so the same spot can be recognised in another image. This repository is magic-leap-community's copy on Hugging Face, and the card cites the SuperPoint paper by DeTone, Malisiewicz and Rabinovich. The card says it is a fully-convolutional network trained in a self-supervised way.
intended_use: The card says the model on its own has limited use, but can serve as a feature extractor for other tasks such as homography estimation and image matching.
what_sets_it_apart: The card says the interest points it detects are repeatable under homographic transformations, that is, they are found again when the view of a flat scene changes. The quoted paper abstract adds that it introduces Homographic Adaptation, a multi-scale, multi-homography approach for boosting that repeatability.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: keypoint-detection
  library: transformers
hub_signals:
  downloads_30d: 18913
  likes: 28
  last_modified: '2025-01-01'
  observed_at: '2026-11-21'
  source: https://huggingface.co/magic-leap-community/superpoint
aliases:
- magic-leap-community/superpoint
lineage:
  parents: []
  children: []
quotes:
  summary: This model is the result of a self-supervised training of a fully-convolutional network for interest point detection and description.
  intended_use: The use of the model in its own is limited, but it can be used as a feature extractor for other tasks such as homography estimation, image matching, etc.
  what_sets_it_apart: We introduce Homographic Adaptation, a multi-scale, multi-homography approach for boosting interest point detection repeatability
tags:
- keypoint-detection
- superpoint
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/magic-leap-community/superpoint
  title: magic-leap-community/superpoint — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/magic-leap-community/superpoint/blob/main/config.json
  title: magic-leap-community/superpoint — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1712.07629
  title: arXiv:1712.07629
  trust: 0.95
---

## Summary

SuperPoint is a model that finds interest points in an image, meaning distinctive spots such as corners, and produces a descriptor for each one so the same spot can be recognised in another image. This repository is magic-leap-community's copy on Hugging Face, and the card cites the SuperPoint paper by DeTone, Malisiewicz and Rabinovich. The card says it is a fully-convolutional network trained in a self-supervised way.

## Intended Use

The card says the model on its own has limited use, but can serve as a feature extractor for other tasks such as homography estimation and image matching.

## What Sets It Apart

The card says the interest points it detects are repeatable under homographic transformations, that is, they are found again when the view of a flat scene changes. The quoted paper abstract adds that it introduces Homographic Adaptation, a multi-scale, multi-homography approach for boosting that repeatability.
