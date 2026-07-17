---
id: architecture-era/mlp-backprop
type: architecture-era
slug: mlp-backprop
schema_version: 1.0
title: "Multilayer Perceptron & Backpropagation"
name: "Multilayer Perceptron (Backpropagation)"
era: early
era_range: "1986–2011"
year: 1986
family: classical-nn
core_innovation: "Stacked non-linear layers trained end-to-end by propagating error gradients backward through the network."
description: "Hidden layers plus the backpropagation algorithm — the recipe that made deep networks trainable."
key_features: ["Hidden layers with non-linear activations", "Backpropagation via the chain rule", "Universal function approximation", "Gradient-descent training"]
key_models: ["NETtalk (1987)", "LeCun handwritten-digit nets (1989)"]
paper_title: "Learning Representations by Back-Propagating Errors"
paper_url: "https://doi.org/10.1038/323533a0"
influenced_by: [architecture-era/perceptron]
led_to: [architecture-era/cnn, architecture-era/rnn-lstm]
tags: [mlp, backpropagation, classical-nn, foundational]
updated: 2026-07-17
sources:
  - { url: "https://doi.org/10.1038/323533a0", title: "Learning Representations by Back-Propagating Errors (Rumelhart, Hinton & Williams, 1986, Nature)", trust: 0.9 }
---

## What Changed
The perceptron could only draw straight lines. Stacking neurons into hidden layers gives a network the
power to represent curved, compositional decision surfaces — a two-layer network with a non-linear
activation is a *universal approximator*. The missing piece was how to train the inner weights, which
have no direct target to compare against. Rumelhart, Hinton, and Williams popularised the answer:
**backpropagation**, the systematic application of the chain rule to compute the gradient of a scalar
loss with respect to every weight in one backward sweep. Crucially, a hidden unit needs a *smooth*
activation (sigmoid, tanh) rather than the perceptron's hard step so that gradients can flow. This
turned a network of neurons into a differentiable function optimisable by gradient descent, solved XOR,
and set the template — forward pass, loss, backward pass, update — that every deep model since follows.

## Key Ideas
- **Hidden layers** — intermediate non-linear representations let the network compose features and fit
  functions no single linear unit can.
- **Backpropagation** — reuse the chain rule to compute all weight gradients in a single backward pass,
  cost comparable to the forward pass; this efficiency is what makes deep training feasible.
- **Universal approximation** — a sufficiently wide one-hidden-layer network can approximate any
  continuous function on a compact domain (an existence result, not a training guarantee).
- **Differentiable activations** — replacing the step with sigmoid/tanh is what lets error signals
  propagate; the choice of activation shapes gradient flow and vanishing-gradient behaviour.
