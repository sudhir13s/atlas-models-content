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
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vitpose_backbone/modeling_vitpose_backbone.py
  summary: ViTPose+ works top-down. A separate person detector finds each person, and the crop around one person is read by a plain vision transformer whose feed-forward layers keep a small part for each training dataset. A decoder of two upsampling layers turns the features into one heatmap per body keypoint.
  side_into: block-ffn
  stages:
  - id: dataset-index
    role: input
    title: Dataset index
    details:
    - 0 to 5 · chosen by the user
    note: The card says this is a mixture-of-experts design and that a dataset index from 0 to 5 must be given for each image. It picks which expert part every feed-forward layer uses; there is no learned router.
    side: true
  - id: crop
    role: input
    title: Person crop
    details:
    - 256 × 192 pixels
    note: The card's example finds people with a separate detector (RT-DETR) and passes their boxes; each box is cropped and resized.
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 16 × 16 pixel patches · width 768
    note: A convolution turns each patch into one vector and learned position embeddings are added.
  - id: block-attention
    role: attention
    title: Self-attention
    details:
    - 12 heads · norm before
    frame: Encoder block
    repeat: 12
  - id: block-ffn
    role: experts
    title: Shared + expert feed-forward
    details:
    - 576 shared + 192 from 1 of 6 experts
    note: The feed-forward layer's output is split. 576 of its 768 numbers come from a part shared by all datasets, the other 192 from the expert part of the chosen dataset.
    frame: Encoder block
  - id: norm
    role: normalisation
    title: Final LayerNorm
    details:
    - back to a 16 × 12 feature map
  - id: decoder
    role: feedForward
    title: Classic decoder
    details:
    - 2 transposed convs · BatchNorm, ReLU
    - 1 × 1 convolution
    note: Two transposed convolutions each double the map size; a 1 × 1 convolution gives one channel per keypoint.
  - id: heatmaps
    role: outputHead
    title: Keypoint heatmaps
    details:
    - 17 COCO keypoints · 64 × 48
    note: Each map is high where its keypoint is likely. Post-processing reads the peak of each map back into image coordinates.
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
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vitpose/modeling_vitpose.py
  title: 'transformers/models/vitpose/modeling_vitpose.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vitpose_backbone/modeling_vitpose_backbone.py
  title: 'transformers/models/vitpose_backbone/modeling_vitpose_backbone.py: backbone with dataset experts (Hugging Face Transformers)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vitpose_backbone/configuration_vitpose_backbone.py
  title: 'transformers/models/vitpose_backbone/configuration_vitpose_backbone.py: backbone defaults (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

ViTPose+ base is a human pose estimation model: given an image of a person, it locates key body joints, called keypoints. The card describes ViTPose+ as a vision transformer foundation model for generic body pose estimation, developed by the ViTAE-Transformer team, and this checkpoint is the version ported to Hugging Face Transformers and published under the usyd-community account.

## Intended Use

The card lists estimating human poses in images or videos, such as the head, shoulders, elbows, wrists, hips, knees and ankles, and recognizing actions from poses over time. It also lists surveillance and monitoring of behavior, fitness apps that give feedback on exercise form, and gaming and animation systems.

## What Sets It Apart

The card describes plain, non-hierarchical vision transformers used as backbones to extract features for a given person, with a lightweight decoder for pose estimation. It says the design can be scaled from 100M to 1B parameters, is flexible in attention type, input resolution and training strategy, and lets knowledge from large models be transferred to small ones through a knowledge token.

## Limitations

The card says the potential of ViTPose is not fully explored with more advanced technologies such as complex decoders or feature pyramid structures, and that applying it to animal pose estimation and face keypoint detection is left as future work.
