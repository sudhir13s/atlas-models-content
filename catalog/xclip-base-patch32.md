---
id: model/xclip-base-patch32
type: model
slug: xclip-base-patch32
schema_version: 1.1
title: xclip-base-patch32
organization: organization/microsoft
release_date: '2022-08-25'
release_year: 2022
release_month: 8
license: license/mit
model_category: vision
tasks:
- task/video-understanding
parameters_active_b: 0.2
total_parameters: 196585855
summary: X-CLIP is a video-language model from Microsoft that scores how well a text fits a video. This is the base-sized version with a patch resolution of 32, trained fully supervised on the Kinetics-400 video dataset using 8 frames per video at 224 by 224 pixels. It comes from the paper "Expanding Language-Image Pretrained Models for General Video Recognition". The Hugging Face team wrote the card, because the X-CLIP team did not.
intended_use: The card says the raw model can be used to determine how well text goes with a given video, and points to the model hub for versions fine-tuned on other tasks. It lists zero-shot, few-shot and fully supervised video classification, and video-text retrieval, as tasks it enables.
what_sets_it_apart: The card describes X-CLIP as a minimal extension of CLIP, an image-text model, for general video-language understanding. It is trained in a contrastive way on pairs of a video and a text, so that matching pairs score higher than mismatched ones.
running_it: The card says training used 8 frames per video at 224 by 224 pixels. During validation, the shorter edge of each frame is resized, the frame is center-cropped to a fixed size and normalized with the ImageNet mean and standard deviation.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 8
  kv_heads: 8
  head_size: 64
  hidden_size: 512
  intermediate_size: 2048
  vocabulary_size: 49408
  activation_function: quick_gelu
  normalization: LayerNorm
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/x_clip/modeling_x_clip.py
  summary: Eight frames are each read by a CLIP-style vision transformer whose frames exchange one message token per block; a small transformer then joins the frames into one video vector. The text (left) is encoded by a CLIP text transformer and adjusted by a prompt generator that looks at the video, and the model scores how well each text matches the video.
  side_into: similarity
  stages:
  - id: text
    role: input
    title: Text
    details:
    - for example a class name
    side: true
  - id: text-embedding
    role: embedding
    title: Token + position embedding
    details:
    - width 512 · 77 positions
    side: true
  - id: text-block
    role: attention
    title: Attention + feed-forward
    details:
    - 8 heads · causal
    frame: Text block
    repeat: 12
    side: true
  - id: text-head
    role: projector
    title: End token + projection
    details:
    - one vector per text · 512
    note: The vector at the end-of-text token stands for the whole text; a linear layer maps it to the shared space.
    side: true
  - id: prompt
    role: attention
    title: Video-specific prompt
    details:
    - 2 layers · text reads the frames
    note: The text vector attends over the frames' patch features (averaged over the frames) and the result, scaled by a learned factor, is added to it.
    side: true
  - id: frames
    role: input
    title: Video frames
    details:
    - 8 frames · 224 × 224 pixels
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 32 × 32 pixel patches · width 768
    note: Each frame becomes 7 × 7 patch vectors plus a class token, with position embeddings added.
  - id: message
    role: attention
    title: Cross-frame message
    details:
    - one token per frame · attention
    note: Each frame's class token is turned into a message token; the message tokens of all frames attend to each other, and each frame's own message joins its patches for this block.
    frame: Frame block
    repeat: 12
  - id: frame-block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 12 heads · within the frame
    frame: Frame block
  - id: frame-vector
    role: projector
    title: Class token + projection
    details:
    - one vector per frame · 512
  - id: mit
    role: attention
    title: Multi-frame integration
    details:
    - 1 layer · 8 heads · then average
    note: Frame position embeddings are added and one transformer layer mixes the 8 frame vectors; the average is the video vector.
  - id: similarity
    role: outputHead
    title: Video-text match score
    details:
    - cosine similarity × learned scale
    note: Both vectors are scaled to length one and multiplied, then scaled by a learned number. A softmax over several texts gives class probabilities.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: video-classification
  library: transformers
hub_signals:
  downloads_30d: 61365
  likes: 126
  last_modified: '2024-02-04'
  observed_at: '2026-11-21'
  source: https://huggingface.co/microsoft/xclip-base-patch32
aliases:
- microsoft/xclip-base-patch32
lineage:
  parents: []
  children: []
quotes:
  summary: This model was trained using 8 frames per video, at a resolution of 224x224.
  intended_use: You can use the raw model for determining how well text goes with a given video.
  what_sets_it_apart: The model is trained in a contrastive way on (video, text) pairs.
  running_it: During validation, one resizes the shorter edge of each frame, after which center cropping is performed to a fixed-size resolution (like 224x224).
tags:
- video-classification
- xclip
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/microsoft/xclip-base-patch32
  title: microsoft/xclip-base-patch32 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/microsoft/xclip-base-patch32/blob/main/config.json
  title: microsoft/xclip-base-patch32 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2208.02816
  title: arXiv:2208.02816
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/x_clip/modeling_x_clip.py
  title: 'transformers/models/x_clip/modeling_x_clip.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

X-CLIP is a video-language model from Microsoft that scores how well a text fits a video. This is the base-sized version with a patch resolution of 32, trained fully supervised on the Kinetics-400 video dataset using 8 frames per video at 224 by 224 pixels. It comes from the paper "Expanding Language-Image Pretrained Models for General Video Recognition". The Hugging Face team wrote the card, because the X-CLIP team did not.

## Intended Use

The card says the raw model can be used to determine how well text goes with a given video, and points to the model hub for versions fine-tuned on other tasks. It lists zero-shot, few-shot and fully supervised video classification, and video-text retrieval, as tasks it enables.

## What Sets It Apart

The card describes X-CLIP as a minimal extension of CLIP, an image-text model, for general video-language understanding. It is trained in a contrastive way on pairs of a video and a text, so that matching pairs score higher than mismatched ones.

## Running It

The card says training used 8 frames per video at 224 by 224 pixels. During validation, the shorter edge of each frame is resized, the frame is center-cropped to a fixed size and normalized with the ImageNet mean and standard deviation.
