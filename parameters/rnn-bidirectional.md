---
id: parameter/rnn-bidirectional
type: parameter
slug: rnn-bidirectional
schema_version: 1.0
title: "Bidirectional"
name: bidirectional
priority: 5
importance: important
applies_to: [rnn]
range: "unidirectional (1×) → bidirectional (2×)"
controls: "Whether the layer runs a second, backward recurrence in addition to the forward one, so each timestep sees both past and future context."
formula: "2× the layer's parameters — a full independent forward and backward RNN"
if_increased: "Each position gets both left and right context, improving accuracy on tasks that see the whole sequence — but it exactly doubles the layer's parameters."
if_decreased: "A single forward pass is half the size and works for streaming/causal tasks, but each step sees only the past, not the future."
tags: [rnn, lstm, bidirectional, context, parameters]
updated: 2026-07-19
sources:
  - { url: "https://ieeexplore.ieee.org/document/650093", title: "Bidirectional Recurrent Neural Networks (Schuster & Paliwal, 1997)", trust: 0.9 }
  - { url: "https://colah.github.io/posts/2015-08-Understanding-LSTMs/", title: "Understanding LSTM Networks (Olah, 2015)", trust: 0.9 }
---

## Definition
A bidirectional RNN runs **two independent recurrences** over the sequence — one left-to-right and one
right-to-left — and concatenates their hidden states at each position. Every timestep's output then
depends on **both past and future** context, rather than only what came before.

Because the backward pass is a full separate RNN with its own weights, turning on `bidirectional`
**exactly doubles** the layer's parameter count.

## What to look for
Bidirectionality is only usable when the full sequence is available up front:

- **Good fit:** tagging, named-entity recognition, encoding for translation — the whole input is known,
  and future context genuinely helps.
- **Impossible:** streaming, autoregressive generation, real-time decoding — you cannot read the future
  in a causal task.

The output dimension also doubles (forward ∥ backward concatenated), which enlarges whatever layer
consumes it — a second, indirect cost beyond the 2× on the RNN itself.

## Calculation
Bidirectional wraps two copies of the same layer, so the multiplier is simply 2:

$$P_\text{bi} = 2 \cdot P_\text{uni} = 2 \cdot G\,h\,(x + h + 1)$$

The concatenated output has size `2h` instead of `h`, so any downstream layer's input dimension also
doubles — count that separately.

## Example
A unidirectional LSTM with `input_size = 100`, `hidden_size = 256` is $4 \times 256 \times (100 + 256
+ 1) = 365\,568$ parameters. Make it bidirectional and it becomes $2 \times 365\,568 = 731\,136$ — a
clean doubling. Its output grows from 256 to 512 dimensions, so a following 256-unit projection also
doubles its input weights, from $256 \times 256$ to $512 \times 256$.
