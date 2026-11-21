---
id: concept/layer-normalization
type: concept
slug: layer-normalization
schema_version: 1.2
title: Layer Normalization
category: normalization
code: LayerNorm
aliases: []
order: 2
short_description: Subtracts the mean and divides by the standard deviation over a layer's summed inputs.
example_models:
- GPT-2
- BERT
- BLOOM
- OPT
- Pythia
- Falcon
tags:
- normalization
- layernorm
updated: '2026-11-21'
sources:
- url: https://arxiv.org/abs/1607.06450
  title: Layer Normalization (arXiv:1607.06450)
  trust: 0.95
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (arXiv:1706.03762)
  trust: 0.95
- url: https://arxiv.org/abs/1910.07467
  title: Root Mean Square Layer Normalization (arXiv:1910.07467)
  trust: 0.95
- url: https://huggingface.co/facebook/opt-125m/blob/main/config.json
  title: facebook/opt-125m config.json
  trust: 0.9
---

## What It Is

Computes the mean and variance over all the summed inputs to the neurons of a layer, on a single training case, and normalizes with them. Each neuron then gets its own learned gain and bias. Introduced by Jimmy Lei Ba, Jamie Ryan Kiros and Geoffrey Hinton in 2016; the original Transformer applies it after each sub-layer.

## Why It Matters

Unlike batch normalization, it does not depend on the mini-batch and performs the same computation at training and test time. The authors report it stabilizes hidden-state dynamics in recurrent networks and reduces training time.

## Trade-off

The RMSNorm authors report that LayerNorm's computational overhead slows networks down. They hypothesize that its re-centering step is dispensable, and report RMSNorm reduces running time by 7% to 64% at comparable performance.
