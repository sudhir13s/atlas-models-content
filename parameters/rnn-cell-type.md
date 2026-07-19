---
id: parameter/rnn-cell-type
type: parameter
slug: rnn-cell-type
schema_version: 1.0
title: "Cell Type (RNN / GRU / LSTM)"
name: rnn_type
priority: 3
importance: critical
applies_to: [rnn]
range: "vanilla RNN (1 gate) → GRU (3) → LSTM (4)"
controls: "Which recurrent cell each layer uses. The cell determines the number of gate matrices, which is a flat multiplier on the layer's entire parameter count."
formula: "params ≈ gate_count × (h² + h·input + h); vanilla=1, GRU=3, LSTM=4"
if_increased: "A more expressive cell (GRU → LSTM) adds gates that better control memory over long sequences, but each gate is a full weight block, scaling the layer up."
if_decreased: "A simpler cell (LSTM → GRU → RNN) is cheaper and faster, but weaker at preserving information across long time spans."
tags: [rnn, lstm, gru, gates, cell, parameters]
updated: 2026-07-19
sources:
  - { url: "https://www.bioinf.jku.at/publications/older/2604.pdf", title: "Long Short-Term Memory (Hochreiter & Schmidhuber, 1997)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1412.3555", title: "Empirical Evaluation of Gated Recurrent Neural Networks (Chung et al., 2014)", trust: 0.9 }
---

## Definition
The cell type is which recurrent unit a layer uses — a **vanilla RNN**, a **GRU** (gated recurrent
unit), or an **LSTM** (long short-term memory). They differ in how many **gates** they contain, and
each gate is a full weight block, so the cell type acts as a flat multiplier on the whole layer.

- Vanilla RNN: 1 transform (no gating).
- GRU: 3 gates (reset, update, candidate).
- LSTM: 4 gates (input, forget, cell/candidate, output).

## What to look for
The choice is a capacity-vs-cost and long-memory tradeoff:

- **LSTM** (4 gates) handles long-range dependencies best — the forget/input gates explicitly control
  what to keep and discard — at the highest parameter cost.
- **GRU** (3 gates) merges some of LSTM's gating, giving ~75% of the parameters and often comparable
  quality, faster to train.
- **Vanilla RNN** (1 gate) is cheapest but suffers badly from vanishing gradients on long sequences.

## Calculation
The cell sets the gate count `G`, a leading multiplier on the per-layer formula:

$$P_\text{layer} = G\big[h \cdot x + h \cdot h + h\big], \qquad G = \begin{cases}1 & \text{RNN}\\ 3 & \text{GRU}\\ 4 & \text{LSTM}\end{cases}$$

So an LSTM layer has exactly **4×** the parameters of a vanilla RNN of the same width, and a GRU has
**3×**. The per-gate shape `h × (x + h)` plus bias is identical across cells; only how many of them
you stack changes.

## Example
At `input_size = 100`, `hidden_size = 256`, one gate block is $256 \times (100 + 256 + 1) = 91\,392$.
A vanilla RNN uses one: **91,392**. A GRU uses three: **274,176**. An LSTM uses four: **365,568**.
Same width, same input — the cell type alone spans a 4× range in parameter count, which is why it is
one of the most consequential structural choices for a recurrent model.
