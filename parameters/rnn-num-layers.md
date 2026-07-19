---
id: parameter/rnn-num-layers
type: parameter
slug: rnn-num-layers
schema_version: 1.0
title: "Number of Layers (Stacked Depth)"
name: num_layers
priority: 2
importance: critical
applies_to: [rnn]
range: "1 → 4"
controls: "How many recurrent layers are stacked, each feeding its hidden-state sequence to the next. Depth lets the network build a hierarchy of temporal features."
formula: "total = Σ per-layer params; layers beyond the first take input_size = hidden_size"
if_increased: "A deeper temporal feature hierarchy and more capacity, but linearly more parameters and harder optimization (recurrent depth compounds vanishing gradients)."
if_decreased: "Cheaper and easier to train, but a single recurrence level captures a shallower notion of sequence structure."
tags: [rnn, lstm, recurrent, depth, structure, parameters]
updated: 2026-07-19
sources:
  - { url: "https://www.bioinf.jku.at/publications/older/2604.pdf", title: "Long Short-Term Memory (Hochreiter & Schmidhuber, 1997)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.3215", title: "Sequence to Sequence Learning with Neural Networks (Sutskever et al., 2014)", trust: 0.9 }
---

## Definition
`num_layers` is how many recurrent layers are **stacked**: the first layer reads the input sequence,
and each subsequent layer consumes the previous layer's hidden-state sequence as its input. This
"deep RNN" lets the network learn a hierarchy of temporal patterns.

Depth multiplies parameters — the total is the sum over layers — with an important twist: **every
layer after the first takes `input_size = hidden_size`**, since its input is the prior layer's state.

## What to look for
Recurrent networks stay shallow compared to CNNs, because depth in time is already hard to optimize:

- 1 layer: many simple sequence tasks.
- 2–4 layers: standard for translation and language modeling (Seq2Seq used 4).

Deeper stacks compound the vanishing-gradient problem *across layers* on top of *across time*, so
gains fall off quickly past ~4 layers — one reason recurrent models rarely went as deep as CNNs.

## Calculation
Depth is the summation bound over per-layer costs. Because upper layers see `x = h`, they are larger
than the first:

$$P_\text{total} = \underbrace{4h(x + h + 1)}_{\text{layer 1}} + (L-1)\cdot\underbrace{4h(h + h + 1)}_{\text{layers 2..L}}$$

Each added layer contributes roughly $4h(2h+1) \approx 8h^2$ — a linear growth in `L`, but each
term is the *larger* upper-layer cost, not the first layer's.

## Example
Stack two LSTM layers with `input_size = 100`, `hidden_size = 256`. Layer 1: $4 \times 256 \times
(100 + 256 + 1) = 365\,568$. Layer 2 (its input is now 256): $4 \times 256 \times (256 + 256 + 1) =
525\,312$. Total $= 890\,880$ — and note the *deeper* layer is the larger one, because its input
dimension grew from 100 to 256 to match the hidden size.
