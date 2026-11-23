---
id: model/deepseek-ocr
type: model
slug: deepseek-ocr
schema_version: 1.1
title: DeepSeek-OCR
organization: organization/deepseek
release_date: '2025-10-17'
release_year: 2025
release_month: 10
license: license/mit
model_category: documents
tasks:
- task/ocr
- task/vision-language-models
total_parameters: 3336106240
context_window_tokens: 8192
summary: DeepSeek-OCR is a model from DeepSeek-AI that reads an image of a document and writes out the text it contains, a task called optical character recognition (OCR). It is a vision-language model, meaning it takes an image and a text prompt and replies in text. Its paper presents it as an initial investigation into compressing long text into images, so that fewer tokens (the small pieces a model reads) are needed.
intended_use: The card shows it reading text from images, either as plain text (the prompt "Free OCR") or by converting a document page to Markdown. The paper adds that it can generate training data for language models and vision-language models, and names research on compressing long contexts as a direction.
what_sets_it_apart: The paper describes two parts. DeepEncoder turns a high-resolution page into a small, manageable number of vision tokens, and the decoder is named DeepSeek3B-MoE-A570M (MoE stands for mixture of experts). The authors report that the model decodes text well when the text has up to 10 times as many tokens as the image does, and that accuracy drops at a 20-times ratio.
running_it: 'The card shows inference with Hugging Face Transformers on NVIDIA GPUs, with pinned package versions and flash-attn installed. It lists five input-size modes set through the base_size, image_size and crop_mode settings: Tiny, Small, Base, Large and Gundam.'
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 10
  kv_heads: 10
  head_size: 128
  hidden_size: 1280
  intermediate_size: 6848
  context_window_tokens: 8192
  vocabulary_size: 129280
moe_config:
  expert_count: 64
  active_experts: 6
  shared_expert: true
pipeline:
  source: https://arxiv.org/abs/2510.18234
  summary: A page image goes through DeepEncoder, a window-attention encoder (SAM), a compressor that keeps one token in sixteen, and a global-attention encoder (CLIP). A linear projector maps the image tokens to the decoder's width, and a mixture-of-experts (MoE) language model reads them with the text prompt and writes the page text.
  side_into: merge
  stages:
  - id: prompt
    role: tokenizer
    title: Text prompt tokens
    side: true
  - id: text-embedding
    role: embedding
    title: Token embedding
    details:
    - width 1280
    side: true
  - id: image
    role: input
    title: Page image
    details:
    - 1024 × 1024 pixels in Base mode
    note: The card and paper list several input sizes; Base mode is 1024 by 1024 pixels, which gives 4,096 patches of 16 by 16 pixels.
  - id: sam
    role: visionEncoder
    title: SAM-base encoder
    details:
    - 12 layers · mostly window attention
    - width 768
    note: About 80 million parameters. Most layers attend only within local windows, so the many patch tokens of a large page stay affordable; layers 3, 6, 9 and 12 attend globally.
  - id: compressor
    role: projector
    title: Token compressor
    details:
    - two convolutions · 16× fewer tokens
    note: Two convolution layers with stride 2 shrink the token grid, so 4,096 tokens become 256 before the dense global-attention part.
  - id: clip
    role: visionEncoder
    title: CLIP-large encoder
    details:
    - 24 layers · global attention
    - width 1024
    note: About 300 million parameters. Its patch embedding layer is removed because it reads the compressor's tokens, not pixels.
  - id: join
    role: projector
    title: Join CLIP + SAM features
    details:
    - width 1024 + 1024 = 2048
    note: For each token, the CLIP output and the compressed SAM output are placed side by side in one vector.
  - id: projector
    role: projector
    title: Projector
    details:
    - linear · width 2048 → 1280
    note: Maps the image tokens into the space of the decoder's token embeddings.
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the image token positions
    note: The image tokens take the places the prompt reserves for the image, so image and text form one sequence.
  - id: attention
    role: attention
    title: Attention
    details:
    - 10 heads · width 1280
    frame: Decoder block
    repeat: 12
  - id: router
    role: router
    title: Router
    details:
    - picks 6 of 64 experts per token
    frame: Decoder block
    note: Scores the routed experts for each token and sends the token to the six with the highest scores. The configuration makes the first block a plain feed-forward layer instead.
  - id: experts
    role: experts
    title: Experts
    details:
    - 6 routed + 2 shared per token
    frame: Decoder block
    note: Each expert is a small feed-forward network; two shared experts process every token. About 570 million of the decoder's 3 billion parameters are active per token.
  - id: output
    role: outputHead
    title: Output head
    details:
    - logits over 129,280
filters:
  size_category: small
  is_quantized: false
  is_moe: true
  attention_type: MHA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 1799082
  likes: 3467
  last_modified: '2025-11-04'
  observed_at: '2026-11-21'
  source: https://huggingface.co/deepseek-ai/DeepSeek-OCR
aliases:
- deepseek-ai/DeepSeek-OCR
lineage:
  parents: []
  children: []
quotes:
  summary: initial investigation into the feasibility of compressing long contexts via optical 2D mapping
  intended_use: Convert the document to markdown.
  what_sets_it_apart: DeepEncoder serves as the core engine, designed to maintain low activations under high-resolution input while achieving high compression ratios
  running_it: Inference using Huggingface transformers on NVIDIA GPUs.
tags:
- image-text-to-text
- deepseek-vl-v2
- moe
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-OCR
  title: deepseek-ai/DeepSeek-OCR — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-OCR/blob/main/config.json
  title: deepseek-ai/DeepSeek-OCR — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2510.18234
  title: arXiv:2510.18234
  trust: 0.95
- url: https://huggingface.co/deepseek-ai/DeepSeek-OCR/blob/main/modeling_deepseekocr.py
  title: deepseek-ai/DeepSeek-OCR — modeling_deepseekocr.py
  trust: 0.9
---

## Summary

DeepSeek-OCR is a model from DeepSeek-AI that reads an image of a document and writes out the text it contains, a task called optical character recognition (OCR). It is a vision-language model, meaning it takes an image and a text prompt and replies in text. Its paper presents it as an initial investigation into compressing long text into images, so that fewer tokens (the small pieces a model reads) are needed.

## Intended Use

The card shows it reading text from images, either as plain text (the prompt "Free OCR") or by converting a document page to Markdown. The paper adds that it can generate training data for language models and vision-language models, and names research on compressing long contexts as a direction.

## What Sets It Apart

The paper describes two parts. DeepEncoder turns a high-resolution page into a small, manageable number of vision tokens, and the decoder is named DeepSeek3B-MoE-A570M (MoE stands for mixture of experts). The authors report that the model decodes text well when the text has up to 10 times as many tokens as the image does, and that accuracy drops at a 20-times ratio.

## Running It

The card shows inference with Hugging Face Transformers on NVIDIA GPUs, with pinned package versions and flash-attn installed. It lists five input-size modes set through the base_size, image_size and crop_mode settings: Tiny, Small, Base, Large and Gundam.
