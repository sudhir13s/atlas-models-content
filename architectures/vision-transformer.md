---
id: architecture-era/vision-transformer
type: architecture-era
slug: vision-transformer
schema_version: 1.0
title: "Vision Transformer (ViT)"
name: "Vision Transformer"
era: scaling
era_range: "2020–2023"
year: 2020
family: vision
core_innovation: "Treat an image as a sequence of patch embeddings and run a plain transformer over them — no convolutions required."
description: "The architecture that showed a pure transformer, given enough data, matches or beats CNNs on vision."
key_features: ["Image split into fixed patches", "Linear patch embeddings + position embeddings", "Standard transformer encoder", "[CLS] token for classification", "Scales with data and model size"]
key_models: ["ViT-B/L/H (2020)", "DeiT (2021)", "SAM / DINOv2 backbones (2023)"]
paper_title: "An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale"
paper_url: "https://arxiv.org/abs/2010.11929"
influenced_by: [architecture-era/transformer, architecture-era/cnn]
led_to: []
tags: [vit, vision, transformer, patches, scaling]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2010.11929", title: "An Image is Worth 16x16 Words (Dosovitskiy et al., 2020)", trust: 0.95 }
---

## What Changed
Vision had been convolutional for a decade — locality and weight sharing were assumed necessary priors
for images. The Vision Transformer discarded them. It cuts an image into a grid of fixed patches (e.g.
16×16 pixels), linearly projects each patch into a token, adds a positional embedding, and feeds the
resulting sequence into an ordinary transformer encoder — the exact machinery built for language. With
little vision-specific inductive bias, ViT *underperforms* CNNs on small datasets, but Dosovitskiy et
al. showed that with enough pretraining data (tens of millions to hundreds of millions of images) it
**matches or surpasses** the best convolutional networks, and keeps improving as data and model size
grow. The lesson generalised the transformer's reach: attention over tokens is a domain-agnostic
substrate, and the CNN's hard-wired priors can be *learned* from data instead. It descends from both
the transformer (the engine) and the CNN (the task and benchmarks it had to beat), and it opened the
door to unified multimodal models that treat image patches and text tokens the same way.

## Key Ideas
- **Patch tokenization** — an image becomes a short sequence of patch embeddings, making the
  quadratic cost of attention tractable (hundreds of tokens, not tens of thousands of pixels).
- **Minimal inductive bias** — no convolutions or pooling; the model must *learn* locality and
  translation behaviour, which needs more data but generalises further.
- **Data-hungry crossover** — CNNs win in the low-data regime; ViT overtakes them once pretraining
  data is large, illustrating the bias-versus-scale trade-off.
- **Unified substrate** — the same transformer handles patches and words, enabling the multimodal
  models that jointly process vision and language.
