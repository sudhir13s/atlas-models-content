---
id: model/unlimited-ocr
type: model
slug: unlimited-ocr
schema_version: 1.1
title: Unlimited-OCR
organization: organization/baidu
release_date: '2026-06-19'
release_year: 2026
release_month: 6
license: license/mit
model_category: documents
tasks:
- task/ocr
- task/vision-language-models
total_parameters: 3336106240
context_window_tokens: 32768
summary: Unlimited OCR is a document-reading model from Baidu. OCR, optical character recognition, means turning an image of a page into text. The accompanying paper takes DeepSeek OCR as its baseline and says the model can transcribe dozens of pages of documents in a single forward pass under a standard maximum length of 32K.
intended_use: The card shows parsing single images in two configurations, called gundam and base, and parsing multi-page documents and PDFs, where each PDF page is first converted to an image and only the base configuration is used. The paper's authors add that the same attention mechanism can also apply to speech recognition and translation.
what_sets_it_apart: The paper points out that a language-model decoder keeps a growing store of past attention data, the KV cache, which raises memory use and slows generation as the output gets longer. It replaces every attention layer in the decoder with Reference Sliding Window Attention, which the authors say reduces attention computation and keeps the KV cache constant for the whole decoding process. It keeps the high-compression encoder of DeepSeek OCR.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 10
  kv_heads: 10
  head_size: 128
  hidden_size: 1280
  intermediate_size: 6848
  context_window_tokens: 32768
  vocabulary_size: 129280
moe_config:
  expert_count: 64
  active_experts: 6
  shared_expert: true
pipeline:
  source: https://arxiv.org/abs/2606.23050
  summary: Page images go through DeepEncoder, kept from DeepSeek OCR, which turns each 1024 by 1024 page into 256 image tokens. A mixture-of-experts (MoE) language model reads them with the prompt and writes the text; every attention layer uses Reference Sliding Window Attention (R-SWA), so each new token sees all image and prompt tokens but only the last 128 tokens it wrote.
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
  - id: pages
    role: input
    title: Page images
    details:
    - one or many pages
    - 1024 × 1024 each in Base mode
    note: Several pages can be read in one pass in Base mode; single pages can also use the dynamic-resolution Gundam mode.
  - id: sam
    role: visionEncoder
    title: SAM-base encoder
    details:
    - 12 layers · mostly window attention
    - width 768
    note: The first half of DeepEncoder relies on window attention over the full set of image patches, which keeps memory low for high-resolution pages.
  - id: compressor
    role: projector
    title: Token compressor
    details:
    - 16× fewer tokens
    note: Compression at the bridge between the two encoders turns a 1024 by 1024 page into 256 tokens.
  - id: clip
    role: visionEncoder
    title: CLIP-large encoder
    details:
    - 24 layers · global attention
    - width 1024
    note: Global attention is used only on the compressed tokens.
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
    note: The image tokens and the prompt form the reference part of the sequence, which every generated token can see.
  - id: attention
    role: attention
    title: R-SWA attention
    details:
    - 10 heads · window of 128 outputs
    frame: Decoder block
    repeat: 12
    note: Each new token attends to all reference tokens (image and prompt) and to the 128 tokens written just before it. Older output keys and values are dropped, so the cache stays the same size however long the output grows.
  - id: router
    role: router
    title: Router
    details:
    - picks 6 of 64 experts per token
    frame: Decoder block
    note: Sends each token to the six routed experts with the highest scores. The configuration makes the first block a plain feed-forward layer instead.
  - id: experts
    role: experts
    title: Experts
    details:
    - 6 routed + 2 shared per token
    frame: Decoder block
    note: Each expert is a small feed-forward network; two shared experts process every token. The decoder has 3 billion parameters, about 500 million active per token.
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
  downloads_30d: 5600129
  likes: 5023
  last_modified: '2026-10-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/baidu/Unlimited-OCR
aliases:
- baidu/Unlimited-OCR
lineage:
  parents: []
  children: []
quotes:
  summary: Unlimited OCR can transcribe dozens of pages of documents in a single forward pass under a standard maximum length of 32K.
  intended_use: Multi page / PDF only uses base (image_size=1024)
  what_sets_it_apart: we replace all attention layers in the decoder with our proposed Reference Sliding Window Attention (R-SWA)
tags:
- image-text-to-text
- unlimited-ocr
- moe
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/baidu/Unlimited-OCR
  title: baidu/Unlimited-OCR — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/baidu/Unlimited-OCR/blob/main/config.json
  title: baidu/Unlimited-OCR — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2606.23050
  title: arXiv:2606.23050
  trust: 0.95
- url: https://huggingface.co/baidu/Unlimited-OCR/blob/main/modeling_unlimitedocr.py
  title: baidu/Unlimited-OCR — modeling_unlimitedocr.py
  trust: 0.9
---

## Summary

Unlimited OCR is a document-reading model from Baidu. OCR, optical character recognition, means turning an image of a page into text. The accompanying paper takes DeepSeek OCR as its baseline and says the model can transcribe dozens of pages of documents in a single forward pass under a standard maximum length of 32K.

## Intended Use

The card shows parsing single images in two configurations, called gundam and base, and parsing multi-page documents and PDFs, where each PDF page is first converted to an image and only the base configuration is used. The paper's authors add that the same attention mechanism can also apply to speech recognition and translation.

## What Sets It Apart

The paper points out that a language-model decoder keeps a growing store of past attention data, the KV cache, which raises memory use and slows generation as the output gets longer. It replaces every attention layer in the decoder with Reference Sliding Window Attention, which the authors say reduces attention computation and keeps the KV cache constant for the whole decoding process. It keeps the high-compression encoder of DeepSeek OCR.
