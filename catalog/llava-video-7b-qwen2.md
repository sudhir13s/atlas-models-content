---
id: model/llava-video-7b-qwen2
type: model
slug: llava-video-7b-qwen2
schema_version: 3.0
title: LLaVA-Video-7B-Qwen2
organization: organization/lmms-lab
release_date: '2024-09-02'
license: license/apache-2-0
model_category: vision
model_kind: multimodal
tasks:
- task/video-understanding
parameters_active_b: 8.03
total_parameters: 8030348832
context_window_tokens: 32768
base_models:
- name: lmms-lab/llava-onevision-qwen2-7b-si
  address: https://huggingface.co/lmms-lab/llava-onevision-qwen2-7b-si
summary: 'LLaVA-Video-7B-Qwen2 is a video-language model: it takes a video and a text question and answers in text. The card says the LLaVA-Video models are 7B and 72B parameter models trained on the LLaVA-Video-178K and LLaVA-OneVision datasets, based on the Qwen2 language model with a 32K-token context window. This checkpoint was initialized from lmms-lab/llava-onevision-qwen2-7b-si, and the card lists English and Chinese.'
intended_use: The card says the model can interact with images, several images and videos, but is specific to videos. It names no out-of-scope uses.
what_sets_it_apart: The card lists the architecture as SO400M, a vision encoder, combined with the Qwen2 language model. Training used a mixture of 1.6M single-image, multi-image and video examples for one epoch, updating the full model, in bfloat16 precision, on 256 Nvidia A100 GPUs (for the whole model series).
running_it: The card says the model supports at most 64 frames. Its example samples 64 frames evenly from the video, tells the model the video length and the time of each frame in the prompt, and decodes without sampling for up to 4096 new tokens.
fingerprint:
  attention_type: GQA
  num_layers: 28
  attention_heads: 28
  kv_heads: 4
  head_size: 128
  hidden_size: 3584
  intermediate_size: 18944
  vocabulary_size: 152064
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000.0
pipeline:
  source: https://github.com/LLaVA-VL/LLaVA-NeXT/blob/main/llava/model/llava_arch.py
  summary: Up to 64 frames are sampled from the video and each is encoded by a SigLIP vision encoder. A small projector maps the frame features to the language model's width, pooling shrinks each frame to fewer tokens, and the Qwen2 language model reads them together with the question, which names the time of each frame, and writes the answer.
  side_into: merge
  stages:
  - id: text
    role: tokenizer
    title: Text tokens
    details:
    - question + frame times
    note: The card's example prompt states how long the video is, how many frames were sampled and the time of each one.
    side: true
  - id: text-embedding
    role: embedding
    title: Token embedding
    details:
    - width 3584
    side: true
  - id: frames
    role: input
    title: Video frames
    details:
    - up to 64 · 384 × 384 pixels
    note: The card's example samples 64 frames evenly across the whole video.
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 14 × 14 pixel patches · width 1152
    note: Each frame becomes a 27 × 27 grid of patch vectors.
  - id: vision-block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - SigLIP SO400M · 16 heads
    note: The SigLIP encoder has 27 blocks; LLaVA-NeXT removes the last one and its pooling head and takes the patch vectors from block 26.
    frame: Vision block
    repeat: 26
  - id: projector
    role: projector
    title: Projector
    details:
    - linear · GELU · linear
    - width 1152 → 3584
    note: Maps each patch vector into the space of the language model's token embeddings.
  - id: pool
    role: projector
    title: Pool each frame
    details:
    - 27 × 27 → 14 × 14 · bilinear
    note: Each frame's grid is resized to half its side, so a frame costs 196 tokens; a learned newline token is added after each row of the grid.
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the image token position
    note: The frame tokens take the place the prompt reserves with an image token, so frames and text form one sequence.
  - id: language-block
    role: attention
    title: Attention + feed-forward
    details:
    - 28 heads · 4 KV heads · width 3584
    note: The Qwen2-7B decoder; attention is causal, so each position sees only what comes before it.
    frame: Transformer block
    repeat: 28
  - id: output
    role: outputHead
    title: Output head
    details:
    - logits over 152,064
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: video-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 12134
  likes: 127
  last_modified: '2024-10-25'
  observed_at: '2026-11-21'
  source: https://huggingface.co/lmms-lab/LLaVA-Video-7B-Qwen2
aliases:
- lmms-lab/LLaVA-Video-7B-Qwen2
lineage:
  parents: []
  children: []
quotes:
  summary: based on Qwen2 language model with a context window of 32K tokens.
  intended_use: having the ability to interact with images, multi-image and videos, but specific to videos.
  what_sets_it_apart: A mixture of 1.6M single-image/multi-image/video data, 1 epoch, full model
  running_it: This model support at most 64 frames.
tags:
- llava
updated: '2026-11-23'
sources:
- url: https://huggingface.co/lmms-lab/LLaVA-Video-7B-Qwen2
  title: lmms-lab/LLaVA-Video-7B-Qwen2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/lmms-lab/LLaVA-Video-7B-Qwen2/blob/main/config.json
  title: lmms-lab/LLaVA-Video-7B-Qwen2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2410.02713
  title: arXiv:2410.02713
  trust: 0.95
- url: https://github.com/LLaVA-VL/LLaVA-NeXT/blob/main/llava/model/llava_arch.py
  title: 'LLaVA-NeXT/llava/model/llava_arch.py: model code (LLaVA-VL)'
  trust: 0.9
- url: https://github.com/LLaVA-VL/LLaVA-NeXT/blob/main/llava/model/multimodal_encoder/siglip_encoder.py
  title: 'LLaVA-NeXT/llava/model/multimodal_encoder/siglip_encoder.py: vision encoder code (LLaVA-VL)'
  trust: 0.9
---


