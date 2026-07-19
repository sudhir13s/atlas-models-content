---
id: parameter/rnn-hidden-units
type: parameter
slug: rnn-hidden-units
schema_version: 1.0
title: "Hidden Units (hidden_size)"
name: hidden_size
priority: 1
importance: critical
applies_to: [rnn]
range: "128 → 2048"
controls: "The size of the recurrent hidden state carried across timesteps. It is the dominant lever on a recurrent layer's capacity and parameter count."
formula: "params ≈ gates × (h² + h·input + h); the recurrent h² term dominates"
if_increased: "More memory capacity per timestep, but parameters grow with the square of the hidden size — doubling h roughly quadruples the layer."
if_decreased: "A cheaper, lighter layer, but a narrow hidden state that can carry less information forward through the sequence."
tags: [rnn, lstm, recurrent, hidden-state, width, parameters]
updated: 2026-07-19
sources:
  - { url: "https://www.bioinf.jku.at/publications/older/2604.pdf", title: "Long Short-Term Memory (Hochreiter & Schmidhuber, 1997)", trust: 0.95 }
  - { url: "https://colah.github.io/posts/2015-08-Understanding-LSTMs/", title: "Understanding LSTM Networks (Olah, 2015)", trust: 0.9 }
---

## Definition
`hidden_size` (`h`) is the dimensionality of the recurrent **hidden state** — the vector an RNN carries
from one timestep to the next. It is the width of the layer, and the single biggest driver of both its
memory capacity and its parameter count.

Every gate contains a recurrent `h × h` weight block that maps the previous state into the next, so
the parameter count grows with **h²** — width is a quadratic cost.

## What to look for
Hidden size trades capacity against cost, with the `h²` term making large states expensive:

- 128–256: small sequence models (tagging, small language models).
- 512–1024: mid-size sequence-to-sequence and language models.
- 2048: large recurrent models (before transformers largely displaced them).

Because the recurrent term is quadratic, widening the hidden state is far more expensive than
widening the input — the reason deep-but-narrow stacks were often preferred over one very wide layer.

## Calculation
For one LSTM layer with input `x`, hidden `h`, and 4 gates (with biases):

$$P_\text{LSTM} = 4\big[h \cdot x + h \cdot h + h\big] = 4h\,(x + h + 1)$$

The `h²` recurrent term (inside the bracket, once per gate) dominates whenever `h ≥ x`. A vanilla RNN
uses 1 gate and a GRU uses 3, but the quadratic dependence on `h` is the same.

## Example
An LSTM with `input_size = 100`, `hidden_size = 256`: $4 \times 256 \times (100 + 256 + 1) = 365\,568$
parameters. Double the hidden state to 512 and it becomes $4 \times 512 \times (100 + 512 + 1) =
1\,255\,424$ — a ~3.4× jump for a 2× width, exactly the quadratic signature. The recurrent block
$h^2 = 65\,536$ already outweighs the input block $h \cdot x = 25\,600$ at `h = 256`.
