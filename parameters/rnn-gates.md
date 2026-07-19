---
id: parameter/rnn-gates
type: parameter
slug: rnn-gates
schema_version: 1.0
title: "Gates (Gating Structure)"
name: num_gates
priority: 4
importance: important
applies_to: [rnn]
range: "1 (RNN) → 3 (GRU) → 4 (LSTM)"
controls: "How many gating functions a cell computes per step. Each gate is a full input+recurrent weight block that regulates what the hidden state keeps, updates, or exposes."
formula: "each gate = h·input + h·h + h; total layer = num_gates × that block"
if_increased: "More gates give finer control over memory (what to forget, write, and read), improving long-range modeling — but each gate adds a full weight block."
if_decreased: "Fewer gates make the cell cheaper and faster, but coarser control over the hidden state and worse long-term retention."
tags: [rnn, lstm, gru, gates, memory, parameters]
updated: 2026-07-19
sources:
  - { url: "https://colah.github.io/posts/2015-08-Understanding-LSTMs/", title: "Understanding LSTM Networks (Olah, 2015)", trust: 0.9 }
  - { url: "https://www.bioinf.jku.at/publications/older/2604.pdf", title: "Long Short-Term Memory (Hochreiter & Schmidhuber, 1997)", trust: 0.95 }
---

## Definition
A **gate** is a small learned function that outputs values in `[0, 1]` (via a sigmoid) and multiplies
into the hidden or cell state to control information flow. In an LSTM the **forget gate** decides what
to erase, the **input gate** what to write, and the **output gate** what to expose. Gates are how
gated RNNs solve the vanishing-gradient problem that cripples vanilla RNNs.

Each gate reads both the current input `x_t` and previous state `h_{t-1}`, so **each gate is a full
weight block** of shape `h × (x + h)` plus a bias — the number of gates directly scales the layer.

## What to look for
The gate count is fixed by the cell design and is the same lever as cell type, viewed from the
mechanism side:

- **LSTM — 4 gates:** input, forget, candidate (cell), output. A separate cell state `c_t` plus hidden
  state `h_t` gives the strongest long-term memory.
- **GRU — 3 gates:** reset, update, candidate. Merges the cell and hidden state; comparable quality at
  lower cost.
- **Vanilla RNN — 1 transform:** no gating, no `[0,1]` control — information cannot be selectively
  retained.

## Calculation
Every gate has the identical block; the count `G` multiplies it:

$$P_\text{layer} = G\big[\underbrace{h \cdot x}_{\text{input } W_{ih}} + \underbrace{h \cdot h}_{\text{recurrent } W_{hh}} + \underbrace{h}_{\text{bias}}\big]$$

The gates carry all of the layer's weights — there are no non-gate parameters in the recurrence. (In
PyTorch each gate stores two bias vectors rather than one, adding `Gh` more; the single-bias form
above is the standard textbook count.)

## Example
At `hidden_size = 256`, `input_size = 100`, one gate block is $256 \times 100 + 256 \times 256 + 256 =
91\,392$. An LSTM's 4 gates give $4 \times 91\,392 = 365\,568$; the cell's memory power comes entirely
from having four such blocks cooperate. Drop to a GRU's 3 gates and you save one full block (~91K
parameters) per layer while keeping most of the gating benefit.
