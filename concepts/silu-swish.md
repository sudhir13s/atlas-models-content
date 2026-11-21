---
id: concept/silu-swish
type: concept
slug: silu-swish
schema_version: 1.2
title: SiLU / Swish
category: activation
code: silu
aliases:
- swish
order: 1
short_description: The input multiplied by its sigmoid, x·σ(x); Swish adds a scale β inside the sigmoid.
example_models:
- Mistral
- Qwen
- DeepSeek
tags:
- activation
- silu
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/1606.08415
  title: Gaussian Error Linear Units (GELUs) (arXiv:1606.08415)
  trust: 0.95
- url: https://arxiv.org/abs/1702.03118
  title: Sigmoid-Weighted Linear Units for Neural Network Function Approximation in Reinforcement Learning (arXiv:1702.03118)
  trust: 0.95
- url: https://arxiv.org/abs/1710.05941
  title: Searching for Activation Functions (arXiv:1710.05941)
  trust: 0.95
---

## What It Is

The SiLU (Sigmoid Linear Unit) is x·σ(x), the input multiplied by its sigmoid. Hendrycks and Gimpel named it in the 2016 GELU paper, and Elfwing, Uchibe and Doya proposed it in 2017. Swish (Ramachandran, Zoph and Le, 2017) is x·σ(βx), which equals the SiLU when β = 1.

## Why It Matters

The Swish authors report that it tends to work better than ReLU on deeper models. For example, replacing ReLU with Swish improved ImageNet top-1 accuracy by 0.9% for Mobile NASNet-A and 0.6% for Inception-ResNet-v2.

## Trade-off

The comparison with GELU is not one-sided: in the GELU paper's experiments, the SiLU performed worse than the GELU but usually better than ReLU and ELU.
