---
id: parameter-calculator/mlp
type: parameter-calculator
slug: mlp
schema_version: 1.0
title: "Multilayer Perceptron (MLP) Parameters"
arch: mlp
order: 8
color_key: mlp
components:
  - { name: "layer widths (units per layer)", rank: 1, importance: critical, badge: "Per Layer", formula: "params = n_in × n_out + n_out", range: "32 → 4096" }
  - { name: "hidden width", rank: 2, importance: critical, badge: "Structure", formula: "dominates when hidden ≫ input/output (n_hidden² between hidden layers)", range: "64 → 2048" }
  - { name: "network depth (num layers)", rank: 3, importance: critical, badge: "Structure", formula: "total = Σ per-layer params", range: "1 → 8" }
  - { name: "input dimension", rank: 4, importance: critical, badge: "Structure", formula: "sizes the first weight matrix (input × first hidden)", range: "task-dependent" }
  - { name: "output dimension", rank: 5, importance: critical, badge: "Head", formula: "sizes the last weight matrix (last hidden × output)", range: "task-dependent" }
  - { name: "per-layer bias", rank: 6, importance: optional, badge: "Per Layer", formula: "+ n_out per layer", range: "0 or n_out" }
  - { name: "batch/layer normalization (optional)", rank: 7, importance: optional, badge: "Per Layer", formula: "2 × n_out trainable per norm", range: "0 or 2·n_out" }
  - { name: "activation (ReLU / GELU / tanh)", rank: 8, importance: architectural, badge: "Per Layer", formula: "0 params — elementwise nonlinearity", range: "0" }
worked_example:
  label: "MNIST classifier (784 → 256 → 128 → 10)"
  config: { inputDim: 784, hidden1: 256, hidden2: 128, outputDim: 10, bias: true, activation: ReLU, layers: 3 }
  sources:
    - { url: "http://neuralnetworksanddeeplearning.com/chap1.html", title: "Neural Networks and Deep Learning, Ch.1 (Nielsen)", trust: 0.9 }
tags: [mlp, feedforward, dense, parameters]
updated: 2026-07-17
sources:
  - { url: "https://www.deeplearningbook.org/contents/mlp.html", title: "Deep Learning, Ch.6: Deep Feedforward Networks (Goodfellow, Bengio, Courville)", trust: 0.9 }
  - { url: "http://neuralnetworksanddeeplearning.com/chap1.html", title: "Neural Networks and Deep Learning, Ch.1 (Nielsen)", trust: 0.9 }
  - { url: "https://www.cs.cmu.edu/~epxing/Class/10715/reading/Rosenblatt.pdf", title: "The Perceptron (Rosenblatt, 1958)", trust: 0.85 }
---

## Overview
A multilayer perceptron is the simplest parametric neural network: a stack of **fully-connected
(dense) layers**, each computing `y = Wx + b` followed by a nonlinearity. There is no weight
sharing, no recurrence, no attention — every input unit connects to every output unit, so a
layer from `n_in` to `n_out` units owns exactly `n_in × n_out` weights plus `n_out` biases. The
parameter count is therefore completely transparent: it is the sum of those rectangles, layer by
layer.

Because dense layers connect everything to everything, the count grows with the *product* of
adjacent widths. Two hidden layers of width `h` cost `h²` between them, so widening an MLP is
quadratically expensive — the same intuition that drives cost in a transformer's feed-forward
block, which is itself just a two-layer MLP applied per token.

## Components
- **Layer widths** are the whole story: each consecutive pair `(n_in, n_out)` contributes
  `n_in·n_out + n_out`.
- **Hidden width** dominates when hidden layers are wider than the input/output, because the
  `hidden × hidden` matrices between them are the largest rectangles.
- **Depth** adds more matrices; each extra hidden layer of width `h` adds ~`h²` parameters.
- **Input and output dimensions** anchor the first and last matrices; for classification the
  output is small (number of classes), so the first hidden layer usually holds the most weights.
- **Biases** add one parameter per output unit; **normalization layers** add `2·n_out` if used;
  **activations** are parameter-free.

The master formula. For a network with layer sizes `n_0 → n_1 → … → n_L`:

$$\text{Total} = \sum_{\ell=1}^{L} \big(n_{\ell-1}\, n_\ell + n_\ell\big)$$

## Worked Example
An MNIST classifier `784 → 256 → 128 → 10` (`config` above), with biases:

- **layer 1** (`784 → 256`): $784 \times 256 + 256 = 200\,704 + 256 = 200\,960$
- **layer 2** (`256 → 128`): $256 \times 128 + 128 = 32\,768 + 128 = 32\,896$
- **layer 3** (`128 → 10`): $128 \times 10 + 10 = 1\,280 + 10 = 1\,290$

Total $= 200\,960 + 32\,896 + 1\,290 = \mathbf{235\,146}$ parameters. Notice the first layer holds
85% of the count — it is anchored by the large `784`-dimensional input (a flattened `28×28`
image), while the deeper layers shrink toward the 10 output classes. This front-loading is the
opposite of a CNN (where cost grows *deeper*, as channels multiply) and a direct consequence of
having no weight sharing on the input.
