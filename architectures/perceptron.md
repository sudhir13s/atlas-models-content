---
id: architecture-era/perceptron
type: architecture-era
slug: perceptron
schema_version: 1.0
title: "Perceptron"
name: "Perceptron"
era: early
era_range: "1958–1969"
year: 1958
family: classical-nn
core_innovation: "A single linear threshold unit whose weights are adjusted from data — the first trainable classifier."
description: "Rosenblatt's learnable linear threshold neuron, the origin point of trainable neural networks."
key_features: ["Weighted sum of inputs", "Step (threshold) activation", "Error-driven weight update", "Online, mistake-bounded learning"]
key_models: ["Mark I Perceptron (1958)"]
paper_title: "The Perceptron: A Probabilistic Model for Information Storage and Organization in the Brain"
paper_url: "https://doi.org/10.1037/h0042519"
influenced_by: []
led_to: [architecture-era/mlp-backprop]
tags: [perceptron, classical-nn, foundational, linear-classifier]
updated: 2026-07-17
sources:
  - { url: "https://doi.org/10.1037/h0042519", title: "The Perceptron (Rosenblatt, 1958, Psychological Review)", trust: 0.9 }
---

## What Changed
Before the perceptron, a classifier's parameters were set by hand. Frank Rosenblatt's perceptron was
the first model that *learned* its parameters directly from labelled examples. It computes a weighted
sum of its inputs and fires if that sum crosses a threshold: $\hat{y} = \mathbb{1}[\,w^\top x + b > 0\,]$.
When it misclassifies an example, it nudges the weights toward the correct answer
($w \leftarrow w + \eta\,(y - \hat{y})\,x$), and the perceptron convergence theorem guarantees this
process finds a separating boundary in finite steps *if the data is linearly separable*. That single
guarantee — a mechanical procedure that provably learns — is what launched the field. Its equally
important limitation, that a single unit can only carve a linear boundary and therefore cannot
represent XOR (Minsky and Papert, 1969), is exactly what the next era set out to overcome.

## Key Ideas
- **Linear threshold unit** — a neuron is a dot product $w^\top x + b$ followed by a hard step; the
  decision surface is a hyperplane.
- **Error-driven update** — weights change only on mistakes, moving the boundary toward the
  misclassified point; no gradient, no differentiable loss needed.
- **Convergence guarantee** — for linearly separable data the update rule terminates with zero
  training error in a bounded number of mistakes.
- **The XOR wall** — a single perceptron cannot represent non-linearly-separable functions, motivating
  stacked, non-linear layers.
