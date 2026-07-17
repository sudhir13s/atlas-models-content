---
id: architecture-era/resnet
type: architecture-era
slug: resnet
schema_version: 1.0
title: "Residual Networks (ResNet)"
name: "ResNet"
era: foundation
era_range: "2015–2018"
year: 2015
family: vision
core_innovation: "Residual skip connections let layers learn a correction to the identity, making networks of 100+ layers trainable."
description: "The skip-connection idea that broke the depth barrier and became a universal deep-learning building block."
key_features: ["Identity skip connections", "Residual (delta) learning", "Very deep networks (50–1000+ layers)", "Improved gradient flow", "Batch normalization"]
key_models: ["ResNet-50/101/152 (2015)", "ResNeXt (2016)", "Wide ResNet (2016)"]
paper_title: "Deep Residual Learning for Image Recognition"
paper_url: "https://arxiv.org/abs/1512.03385"
influenced_by: [architecture-era/cnn]
led_to: [architecture-era/transformer, architecture-era/vision-transformer]
tags: [resnet, vision, residual, skip-connection, deep-networks]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (He et al., 2015)", trust: 0.95 }
---

## What Changed
Deeper networks should be at least as good as shallower ones — a deep net can always set extra layers
to identity — yet in practice, past a point, adding layers made both training *and* test error worse.
This "degradation" problem, not overfitting, capped useful depth. He et al. fixed it with a tiny
structural change: the **residual block**, where a layer computes $\mathcal{F}(x)$ and the block
outputs $\mathcal{F}(x) + x$ via a skip connection. Now a layer only has to learn the *residual* (the
correction to the identity), and if the best thing to do is nothing, driving $\mathcal{F}$ toward zero
is easy. Practically, the skip is also a gradient highway: backprop flows through the identity path
undiminished, defeating the vanishing-gradient problem. ResNet trained networks of 100+ (even 1000+)
layers, won ImageNet 2015, and its skip connection became one of the most reused primitives in all of
deep learning — the transformer's residual-around-every-sublayer design (and thus ViT) inherits it
directly.

## Key Ideas
- **Residual learning** — parameterise a block as identity plus a learned delta $\mathcal{F}(x)+x$, so
  layers refine rather than replace the signal.
- **Degradation, not overfitting** — the pre-ResNet failure of deep nets was an optimization problem;
  skip connections make the identity easy to represent and recover trainability.
- **Gradient highway** — the additive shortcut passes gradients backward unattenuated, so very deep
  stacks stay trainable.
- **A universal primitive** — residual connections now appear in nearly every deep architecture,
  including every transformer sublayer.
