---
id: model/donut-base-finetuned-docvqa
type: model
slug: donut-base-finetuned-docvqa
schema_version: 3.0
title: donut-base-finetuned-docvqa
organization: organization/naver-clova-ix
release_date: '2022-07-19'
license: license/mit
model_category: documents
model_kind: multimodal
tasks:
- task/document-question-answering
summary: 'Donut base fine-tuned on DocVQA is a document question answering model: given an image of a document and a question about it, it writes the answer as text. The card says this is Donut fine-tuned on DocVQA, a document visual question answering dataset, and that Donut was introduced in the paper OCR-free Document Understanding Transformer. The Hugging Face team wrote the card, not the team that released Donut.'
intended_use: The card says only that the model is fine-tuned on DocVQA and refers readers to the Transformers documentation for code examples. The example questions in its metadata, "What is the invoice number?" and "What is the purchase amount?", show the kind of question it is meant to answer from scans of invoices and contracts.
what_sets_it_apart: The card describes two parts. A vision encoder, a Swin Transformer, turns the image into a set of embeddings, and a text decoder, BART, then writes text one token at a time based on that encoding. The paper title says the approach needs no OCR (optical character recognition), so the page image is read directly rather than first passed through a separate text-extraction step.
fingerprint:
  num_layers: 12
  hidden_size: 1024
  vocabulary_size: 57532
  activation_function: gelu
pipeline:
  source: https://arxiv.org/abs/2111.15664
  summary: A Swin Transformer encoder (left) reads the document image as patches, without a separate text-recognition step. A BART-style text decoder (right) starts from the question as its prompt, reads the image features through cross-attention and writes the answer as tokens that convert to JSON.
  side_into: cross-attention
  stages:
  - id: image
    role: input
    title: Document image
    details:
    - 2560 × 1920 pixels
    side: true
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 4 × 4 pixel patches · width 128
    note: The image is split into non-overlapping 4 by 4 pixel patches, and each becomes one vector.
    side: true
  - id: swin-attention
    role: visionEncoder
    title: Shifted-window attention
    details:
    - + two-layer MLP · window 10
    - 2, 2, 14, 2 blocks per stage
    note: Each Swin block runs self-attention inside local windows, shifted between blocks, followed by a two-layer MLP. The four stages have 4, 8, 16 and 32 heads.
    frame: Swin stage
    repeat: 4
    side: true
  - id: merging
    role: projector
    title: Patch merging
    details:
    - halves the grid · width up to 1024
    note: Between stages, neighbouring patch vectors are merged, so the grid gets coarser and the vectors wider, ending at width 1024.
    frame: Swin stage
    side: true
  - id: prompt
    role: input
    title: Task + question prompt
    details:
    - special tokens · then the question
    note: For DocVQA the question is given as the starting prompt, and the decoder continues the sequence with the answer.
  - id: embedding
    role: embedding
    title: Token embedding
    details:
    - width 1024 · 57,532 tokens
  - id: self-attention
    role: attention
    title: Self-attention
    details:
    - 16 heads · over the tokens so far
    frame: Decoder block
    repeat: 4
    note: The paper uses the first four layers of BART as the decoder to balance speed and accuracy.
  - id: cross-attention
    role: attention
    title: Cross-attention
    details:
    - reads the image features
    frame: Decoder block
  - id: feed-forward
    role: feedForward
    title: Feed-forward
    details:
    - width 4096 · GELU
    frame: Decoder block
  - id: output
    role: outputHead
    title: Output head
    details:
    - logits over 57,532
    note: Tokens are generated one at a time; the finished sequence maps one-to-one to a JSON answer.
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: document-question-answering
  library: transformers
hub_signals:
  downloads_30d: 7392
  likes: 285
  last_modified: '2024-03-09'
  observed_at: '2026-11-21'
  source: https://huggingface.co/naver-clova-ix/donut-base-finetuned-docvqa
aliases:
- naver-clova-ix/donut-base-finetuned-docvqa
lineage:
  parents: []
  children: []
quotes:
  summary: Donut model fine-tuned on DocVQA.
  intended_use: This model is fine-tuned on DocVQA, a document visual question answering dataset.
  what_sets_it_apart: Donut consists of a vision encoder (Swin Transformer) and a text decoder (BART).
tags:
- vision-encoder-decoder
updated: '2026-11-23'
sources:
- url: https://huggingface.co/naver-clova-ix/donut-base-finetuned-docvqa
  title: naver-clova-ix/donut-base-finetuned-docvqa — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/naver-clova-ix/donut-base-finetuned-docvqa/blob/main/config.json
  title: naver-clova-ix/donut-base-finetuned-docvqa — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2111.15664
  title: arXiv:2111.15664
  trust: 0.95
---


