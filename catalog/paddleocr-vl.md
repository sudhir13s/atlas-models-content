---
id: model/paddleocr-vl
type: model
slug: paddleocr-vl
schema_version: 3.0
title: PaddleOCR-VL
organization: organization/paddlepaddle
release_date: '2025-10-16'
license: license/apache-2-0
model_category: documents
model_kind: multimodal
tasks:
- task/ocr
- task/vision-language-models
parameters_active_b: 0.96
total_parameters: 958588736
context_window_tokens: 131072
base_models:
- name: baidu/ERNIE-4.5-0.3B-Paddle
  address: https://huggingface.co/baidu/ERNIE-4.5-0.3B-Paddle
summary: 'PaddleOCR-VL is a document parsing model from PaddlePaddle: it reads an image of a page and recognizes its text, tables, formulas and charts. Its core component is PaddleOCR-VL-0.9B, a vision-language model, meaning a model that reads images and writes text, which joins a NaViT-style dynamic-resolution visual encoder with the ERNIE-4.5-0.3B language model. The card states support for 109 languages.'
intended_use: The card presents it for document parsing at page level and for recognizing individual elements such as text, tables, formulas and charts. It says the content types it handles include handwritten text and historical documents.
what_sets_it_apart: 'The card describes a compact design made for resource-efficient inference: a visual encoder that takes images at dynamic high resolution, paired with a lightweight language model, ERNIE-4.5-0.3B. The card lists the 109 languages as including Chinese, English, Japanese, Latin and Korean, as well as Russian (Cyrillic script), Arabic, Hindi (Devanagari script) and Thai.'
fingerprint:
  attention_type: GQA
  num_layers: 18
  attention_heads: 16
  kv_heads: 2
  head_size: 128
  hidden_size: 1024
  intermediate_size: 3072
  vocabulary_size: 103424
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 500000.0
pipeline:
  source: https://arxiv.org/abs/2510.14528
  summary: Parsing runs in two stages. A layout model (PP-DocLayoutV2) finds the text blocks, tables, formulas and charts on the page and puts them in reading order; each region is then cropped and read by the vision-language model PaddleOCR-VL-0.9B. A post-processing step joins the results into Markdown and JSON.
  stages:
  - id: page
    role: input
    title: Page image
  - id: layout
    role: visionEncoder
    title: Layout detection
    details:
    - RT-DETR · finds and labels regions
    note: The first stage of PP-DocLayoutV2, an RT-DETR object detector, gives each text block, table, formula and chart a box and a class.
  - id: reading-order
    role: attention
    title: Reading order
    details:
    - pointer network · 6 layers
    note: A small network compares every pair of detected regions and decides which comes first, giving the order in which the page is read.
  - id: crop
    role: input
    title: Region images
    details:
    - one crop per region
    note: Each region is cut out of the page at its box and sent to the recognition model on its own.
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 14 × 14 pixel patches · width 1152
    note: The encoder takes each crop at its own resolution and turns every 14 by 14 pixel patch into one vector, in the style of NaViT.
  - id: vision-block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 16 heads · width 1152
    frame: Vision block
    repeat: 27
  - id: projector
    role: projector
    title: Projector
    details:
    - merges 2 × 2 patches · 2-layer MLP
    - width 1152 → 1024
    note: Joins each 2 by 2 group of neighbouring patch vectors into one and maps it with a two-layer network with GELU into the language model's embedding space.
  - id: language-block
    role: attention
    title: Attention + feed-forward
    details:
    - 16 heads · 2 KV heads · width 1024
    frame: Language block
    repeat: 18
    note: The ERNIE-4.5-0.3B language model reads the image tokens with the prompt; positions use a three-dimensional rotary encoding (3D-RoPE).
  - id: text
    role: outputHead
    title: Recognized content
    details:
    - text, table, formula or chart
  - id: post
    role: outputHead
    title: Post-processing
    details:
    - Markdown and JSON
    note: A light step combines the layout, the reading order and each region's recognized content into one structured document.
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: image-text-to-text
  library: PaddleOCR
hub_signals:
  downloads_30d: 18846
  likes: 1688
  last_modified: '2026-11-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/PaddlePaddle/PaddleOCR-VL
aliases:
- PaddlePaddle/PaddleOCR-VL
lineage:
  parents: []
  children: []
quotes:
  summary: integrates a NaViT-style dynamic resolution visual encoder with the ERNIE-4.5-0.3B language model
  intended_use: model tailored for document parsing
  what_sets_it_apart: specifically designed for resource-efficient inference
tags:
- paddleocr-vl
updated: '2026-11-23'
sources:
- url: https://huggingface.co/PaddlePaddle/PaddleOCR-VL
  title: PaddlePaddle/PaddleOCR-VL — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/PaddlePaddle/PaddleOCR-VL/blob/main/config.json
  title: PaddlePaddle/PaddleOCR-VL — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2510.14528
  title: arXiv:2510.14528
  trust: 0.95
---


