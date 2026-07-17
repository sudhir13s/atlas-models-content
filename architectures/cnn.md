---
id: architecture-era/cnn
type: architecture-era
slug: cnn
schema_version: 1.0
title: "Convolutional Neural Networks"
name: "Convolutional Neural Network"
era: foundation
era_range: "1989–2015"
year: 2012
family: vision
core_innovation: "Weight-shared convolutional filters plus pooling exploit spatial locality, cutting parameters and building translation-tolerant features."
description: "From LeNet to AlexNet — the architecture that made GPU-trained deep vision work at scale."
key_features: ["Weight-shared convolution kernels", "Local receptive fields", "Pooling for translation tolerance", "Hierarchical feature learning", "GPU-scale training"]
key_models: ["LeNet-5 (1998)", "AlexNet (2012)", "VGG (2014)", "GoogLeNet (2014)"]
paper_title: "ImageNet Classification with Deep Convolutional Neural Networks (AlexNet)"
paper_url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
influenced_by: [architecture-era/mlp-backprop]
led_to: [architecture-era/resnet, architecture-era/vision-transformer]
tags: [cnn, vision, convolution, alexnet, foundational]
updated: 2026-07-17
sources:
  - { url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html", title: "ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)", trust: 0.95 }
  - { url: "https://doi.org/10.1109/5.726791", title: "Gradient-Based Learning Applied to Document Recognition (LeCun et al., 1998)", trust: 0.9 }
---

## What Changed
A fully connected network treats an image as an unstructured vector: every pixel connects to every
hidden unit, so parameters explode and the model must relearn a pattern separately in every location.
The convolutional network bakes in the geometry of images. A small kernel slides across the image,
sharing the *same* weights at every position — this is a strong prior (features are useful wherever
they appear) that slashes parameter count and gives translation tolerance. Stacking convolution and
pooling builds a hierarchy: edges → textures → parts → objects. LeCun's LeNet proved the idea on
digits in the 1990s, but it was AlexNet in 2012 — deep, ReLU-activated, dropout-regularised, and
trained on two GPUs — that halved the ImageNet error rate overnight and triggered the modern deep
learning boom. It descends directly from the backprop-trained MLP and set up both very deep residual
nets and, eventually, the patch-based Vision Transformer.

## Key Ideas
- **Weight sharing** — one kernel is reused across all spatial positions, so the model learns a
  feature once and detects it everywhere, with far fewer parameters than a dense layer.
- **Local receptive fields** — each unit sees a small neighbourhood; depth grows the effective
  receptive field so deeper layers integrate wider context.
- **Pooling** — downsampling (max/average) adds translation tolerance and shrinks spatial resolution
  while growing channel depth.
- **Hierarchical features** — early layers learn edges and colour blobs, later layers learn object
  parts, an emergent compositional representation.
- **Scale + hardware** — ReLU, dropout, data augmentation, and GPU training (AlexNet) were the
  practical unlocks that turned a decades-old idea into a breakthrough.
