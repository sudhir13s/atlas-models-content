---
id: parameter/rnn-lstm
type: parameter
slug: rnn-lstm
schema_version: 1.0
title: "Recurrent Network / LSTM Parameters"
arch: rnn
order: 5
color_key: rnn
components:
  - { name: "hidden_size (h)", rank: 1, importance: critical, badge: "Per Layer", formula: "params ≈ 4 × (h² + h·input + h); the h² term dominates", range: "128 → 2048" }
  - { name: "input_size (x)", rank: 2, importance: critical, badge: "Per Layer", formula: "each gate has an h × input weight block", range: "embedding / feature dim" }
  - { name: "gate count (LSTM = 4)", rank: 3, importance: architectural, badge: "Per Layer", formula: "×4 for LSTM (i,f,g,o); ×3 for GRU; ×1 for vanilla RNN", range: "1 (RNN) → 4 (LSTM)" }
  - { name: "num_layers (stacked depth)", rank: 4, importance: important, badge: "Structure", formula: "layer 2+ take input_size = hidden_size", range: "1 → 4" }
  - { name: "bidirectional", rank: 5, importance: optional, badge: "Structure", formula: "doubles params (a forward and a backward RNN)", range: "1× or 2×" }
worked_example:
  label: "Single LSTM layer (input 100, hidden 256)"
  config: { inputSize: 100, hiddenSize: 256, gates: 4, numLayers: 1, bidirectional: false, bias: true }
  sources:
    - { url: "https://www.bioinf.jku.at/publications/older/2604.pdf", title: "Long Short-Term Memory (Hochreiter & Schmidhuber, 1997)", trust: 0.95 }
tags: [rnn, lstm, recurrent, parameters]
updated: 2026-07-17
sources:
  - { url: "https://www.bioinf.jku.at/publications/older/2604.pdf", title: "Long Short-Term Memory (Hochreiter & Schmidhuber, 1997)", trust: 0.95 }
  - { url: "https://colah.github.io/posts/2015-08-Understanding-LSTMs/", title: "Understanding LSTM Networks (Olah, 2015)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1412.3555", title: "Empirical Evaluation of Gated Recurrent Neural Networks (Chung et al., 2014)", trust: 0.9 }
---

## Overview
A recurrent layer processes a sequence one step at a time, maintaining a hidden state that carries
information forward. Its parameter count is set entirely by two numbers — the input dimension and the
hidden dimension — and by how many gate matrices the cell uses. A vanilla RNN has one, a GRU has
three, and an **LSTM has four**: the input gate, forget gate, candidate (cell) gate, and output gate.

Each gate is a small linear layer that reads both the current input `x_t` and the previous hidden
state `h_{t-1}`, so each gate owns a weight block of shape `hidden × (input + hidden)` plus a bias.
Crucially, the parameter count is **independent of sequence length**: the same weights are reused at
every timestep. This weight-sharing across time is the recurrent analogue of a CNN's weight-sharing
across space, and it is what lets an LSTM handle arbitrarily long sequences with a fixed budget.

## Components
- **hidden_size (h)** is the dominant lever. Because each gate contains an `h × h` recurrent block,
  the count grows with `h²` — doubling the hidden size roughly quadruples the parameters.
- **input_size (x)** contributes an `h × x` block per gate; for a stacked LSTM, layers beyond the
  first take `x = h` (their input is the previous layer's hidden state).
- **gate count** is architectural: LSTM = 4, GRU = 3, vanilla RNN = 1. This is a flat multiplier on
  the whole layer.
- **num_layers** stacks cells; the first layer sees the true input dimension, every later layer sees
  `hidden_size`.
- **bidirectional** runs a separate forward and backward recurrence and concatenates them, exactly
  doubling the parameter count.

The master formula for one LSTM layer (input `x`, hidden `h`, with biases):

$$P_\text{LSTM} = 4 \big[ h \cdot x + h \cdot h + h \big] = 4h\,(x + h + 1)$$

The four gates share the same shape, hence the leading factor of 4. (PyTorch uses two bias vectors
per gate, adding `4h` more; the single-bias form above is the standard textbook count.) A GRU
replaces the 4 with 3; a vanilla RNN with 1.

## Worked Example
A single LSTM layer with `input_size = 100`, `hidden_size = 256` (`config` above):

$$P = 4 \times 256 \times (100 + 256 + 1) = 4 \times 256 \times 357 = \mathbf{365\,568}$$

Broken out per gate: input-to-hidden $256 \times 100 = 25\,600$; hidden-to-hidden $256 \times 256 =
65\,536$; bias $256$ — that is $91\,392$ per gate, and $4 \times 91\,392 = 365\,568$. Notice the
recurrent term $h^2 = 65\,536$ is more than twice the input term, so widening the hidden state is far
more expensive than widening the input. Stack a second identical LSTM layer and its input becomes
$256$ (not $100$), giving $4 \times 256 \times (256 + 256 + 1) = 525\,312$ — the deeper layer is
larger precisely because its input dimension grew to match the hidden size.
