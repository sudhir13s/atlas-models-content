---
id: model/janus-pro-7b
type: model
slug: janus-pro-7b
schema_version: 1.1
title: Janus-Pro-7B
organization: organization/deepseek
release_date: '2025-01-26'
release_year: 2025
release_month: 1
license: license/mit
model_category: multimodal
tasks:
- task/any-to-any-models
summary: Janus-Pro-7B is a multimodal model from DeepSeek-AI that both understands images and generates them. Its card describes Janus-Pro as an autoregressive framework, one that produces its output piece by piece, built on the DeepSeek-LLM-1.5b-base and DeepSeek-LLM-7b-base language models. It is tagged for text-to-image generation.
what_sets_it_apart: The card says Janus-Pro splits visual encoding into separate pathways for understanding and for generation while still using a single unified transformer. It says this removes a conflict between the two roles of the visual encoder and makes the framework more flexible. For understanding it uses the SigLIP-L vision encoder, which takes 384 by 384 pixel images. For generation it uses the tokenizer from LlamaGen with a downsample rate of 16.
running_it: The card gives no run instructions itself and refers readers to the Janus GitHub repository. It states that use of the models is subject to the DeepSeek Model License, while the code repository is under the MIT License.
pipeline:
  source: https://arxiv.org/abs/2501.17811
  summary: The figure shows image generation. A text prompt goes into a 30-layer transformer, which predicts the image one discrete code at a time; each new code is embedded and fed back (left). A decoder turns the 576 codes into a 384 × 384 image. Images to understand enter the same transformer through a separate encoder, described in the transformer's note.
  side_into: block
  stages:
  - id: codes-so-far
    role: input
    title: Image codes so far
    details:
    - one new code per step
    side: true
  - id: codebook
    role: embedding
    title: Codebook embedding
    details:
    - 16,384 codes · width 8
    note: Each image code is looked up in the codebook of the VQ tokenizer, giving a small vector of width 8.
    side: true
  - id: gen-adaptor
    role: projector
    title: Generation adaptor
    details:
    - two-layer MLP · 8 → 4096
    note: Maps the code vectors into the input space of the language model.
    side: true
  - id: prompt
    role: tokenizer
    title: Text prompt tokens
    details:
    - vocabulary about 100K
  - id: embedding
    role: embedding
    title: Token embedding
    details:
    - width 4096
  - id: block
    role: attention
    title: Attention + feed-forward
    details:
    - 32 heads · width 4096
    note: One autoregressive transformer (DeepSeek-LLM 7B) serves both tasks. For understanding, a SigLIP-Large encoder reads a 384 × 384 image and a two-layer MLP adaptor maps its features into this transformer, whose built-in text head writes the answer.
    frame: Transformer block
    repeat: 30
  - id: image-head
    role: outputHead
    title: Image head
    details:
    - scores over 16,384 codes
    note: A separate prediction head for images picks the next code. The reference script mixes the scores with and without the prompt (guidance weight 5) and samples.
  - id: vq-decoder
    role: autoencoder
    title: VQ decoder
    details:
    - 576 codes (24 × 24) to pixels
    note: After 576 codes, the decoder of the VQ tokenizer, which downsamples images by 16, turns the grid of codes back into an image.
  - id: image
    role: outputHead
    title: Image
    details:
    - 384 × 384 pixels
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: any-to-any
  library: transformers
hub_signals:
  downloads_30d: 17817
  likes: 3727
  last_modified: '2025-02-01'
  observed_at: '2026-11-21'
  source: https://huggingface.co/deepseek-ai/Janus-Pro-7B
aliases:
- deepseek-ai/Janus-Pro-7B
lineage:
  parents: []
  children: []
quotes:
  summary: Janus-Pro is a novel autoregressive framework that unifies multimodal understanding and generation.
  what_sets_it_apart: It addresses the limitations of previous approaches by decoupling visual encoding into separate pathways, while still utilizing a single, unified transformer architecture for processing.
  running_it: Please refer to [**Github Repository**](https://github.com/deepseek-ai/Janus)
tags:
- any-to-any
- multi-modality
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/deepseek-ai/Janus-Pro-7B
  title: deepseek-ai/Janus-Pro-7B — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/Janus-Pro-7B/blob/main/config.json
  title: deepseek-ai/Janus-Pro-7B — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2501.17811
  title: arXiv:2501.17811
  trust: 0.95
- url: https://github.com/deepseek-ai/Janus/blob/main/generation_inference.py
  title: 'Janus/generation_inference.py: reference generation script (DeepSeek)'
  trust: 0.9
---

## Summary

Janus-Pro-7B is a multimodal model from DeepSeek-AI that both understands images and generates them. Its card describes Janus-Pro as an autoregressive framework, one that produces its output piece by piece, built on the DeepSeek-LLM-1.5b-base and DeepSeek-LLM-7b-base language models. It is tagged for text-to-image generation.

## What Sets It Apart

The card says Janus-Pro splits visual encoding into separate pathways for understanding and for generation while still using a single unified transformer. It says this removes a conflict between the two roles of the visual encoder and makes the framework more flexible. For understanding it uses the SigLIP-L vision encoder, which takes 384 by 384 pixel images. For generation it uses the tokenizer from LlamaGen with a downsample rate of 16.

## Running It

The card gives no run instructions itself and refers readers to the Janus GitHub repository. It states that use of the models is subject to the DeepSeek Model License, while the code repository is under the MIT License.
