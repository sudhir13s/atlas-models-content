---
id: model/vitpose-plus-base
type: model
slug: vitpose-plus-base
schema_version: 1.1
title: vitpose-plus-base
organization: organization/usyd-community
release_date: '2025-01-08'
release_year: 2025
release_month: 1
license: license/apache-2-0
model_category: vision
tasks:
- task/keypoint-detection
parameters_active_b: 0.13
total_parameters: 125396497
summary: 'ViTPose+ base is a human pose estimation model: given an image of a person, it locates key body joints, called keypoints. The card describes ViTPose+ as a vision transformer foundation model for generic body pose estimation, developed by the ViTAE-Transformer team, and this checkpoint is the version ported to Hugging Face Transformers and published under the usyd-community account.'
intended_use: The card lists estimating human poses in images or videos, such as the head, shoulders, elbows, wrists, hips, knees and ankles, and recognizing actions from poses over time. It also lists surveillance and monitoring of behavior, fitness apps that give feedback on exercise form, and gaming and animation systems.
what_sets_it_apart: The card describes plain, non-hierarchical vision transformers used as backbones to extract features for a given person, with a lightweight decoder for pose estimation. It says the design can be scaled from 100M to 1B parameters, is flexible in attention type, input resolution and training strategy, and lets knowledge from large models be transferred to small ones through a knowledge token.
limitations: The card says the potential of ViTPose is not fully explored with more advanced technologies such as complex decoders or feature pyramid structures, and that applying it to animal pose estimation and face keypoint detection is left as future work.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: keypoint-detection
  library: transformers
hub_signals:
  downloads_30d: 49355
  likes: 37
  last_modified: '2025-01-08'
  observed_at: '2026-11-21'
  source: https://huggingface.co/usyd-community/vitpose-plus-base
aliases:
- usyd-community/vitpose-plus-base
lineage:
  parents: []
  children: []
quotes:
  summary: The ViTPose model, developed by the ViTAE-Transformer team, is primarily designed for pose estimation tasks.
  intended_use: 'Human Pose Estimation: The model can be used to estimate the poses of humans in images or videos.'
  what_sets_it_apart: It can be scaled up from 100M to 1B
  limitations: However, the potential of ViTPose is not fully explored with more
tags:
- keypoint-detection
- vitpose
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/usyd-community/vitpose-plus-base
  title: usyd-community/vitpose-plus-base — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/usyd-community/vitpose-plus-base/blob/main/config.json
  title: usyd-community/vitpose-plus-base — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2204.12484
  title: arXiv:2204.12484
  trust: 0.95
---

## Summary

ViTPose+ base is a human pose estimation model: given an image of a person, it locates key body joints, called keypoints. The card describes ViTPose+ as a vision transformer foundation model for generic body pose estimation, developed by the ViTAE-Transformer team, and this checkpoint is the version ported to Hugging Face Transformers and published under the usyd-community account.

## Intended Use

The card lists estimating human poses in images or videos, such as the head, shoulders, elbows, wrists, hips, knees and ankles, and recognizing actions from poses over time. It also lists surveillance and monitoring of behavior, fitness apps that give feedback on exercise form, and gaming and animation systems.

## What Sets It Apart

The card describes plain, non-hierarchical vision transformers used as backbones to extract features for a given person, with a lightweight decoder for pose estimation. It says the design can be scaled from 100M to 1B parameters, is flexible in attention type, input resolution and training strategy, and lets knowledge from large models be transferred to small ones through a knowledge token.

## Limitations

The card says the potential of ViTPose is not fully explored with more advanced technologies such as complex decoders or feature pyramid structures, and that applying it to animal pose estimation and face keypoint detection is left as future work.
