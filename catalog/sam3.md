---
id: model/sam3
type: model
slug: sam3
schema_version: 1.1
title: sam3
organization: organization/meta
release_date: '2025-11-07'
release_year: 2025
release_month: 11
license_note: other
model_category: vision
tasks:
- task/image-segmentation
total_parameters: 859922360
summary: SAM 3 is an image and video segmentation model published on Hugging Face under the facebook organization. Segmentation means outlining the exact pixels of an object. The card says it can detect, segment and track objects in images and videos using text prompts or visual prompts such as points, boxes and masks.
what_sets_it_apart: The card says that, compared with its predecessor SAM 2, SAM 3 can segment every instance of an open-vocabulary concept, meaning any concept named by a short text phrase or shown by example images. The card says it handles a much larger set of such prompts than earlier work.
filters:
  size_category: tiny
  is_gated: true
  pipeline_tag: mask-generation
  library: transformers
hub_signals:
  downloads_30d: 1895035
  likes: 7148
  last_modified: '2025-11-20'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/sam3
aliases:
- facebook/sam3
lineage:
  parents: []
  children: []
quotes:
  summary: SAM 3 is a unified foundation model for promptable segmentation in images and videos.
  what_sets_it_apart: SAM 3 introduces the ability to exhaustively segment all instances of an open-vocabulary concept specified by a short text phrase or exemplars.
tags:
- mask-generation
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/sam3
  title: facebook/sam3 — model page (Hugging Face)
  trust: 0.9
---

## Summary

SAM 3 is an image and video segmentation model published on Hugging Face under the facebook organization. Segmentation means outlining the exact pixels of an object. The card says it can detect, segment and track objects in images and videos using text prompts or visual prompts such as points, boxes and masks.

## What Sets It Apart

The card says that, compared with its predecessor SAM 2, SAM 3 can segment every instance of an open-vocabulary concept, meaning any concept named by a short text phrase or shown by example images. The card says it handles a much larger set of such prompts than earlier work.
