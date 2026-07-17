---
id: architecture-era/rnn-lstm
type: architecture-era
slug: rnn-lstm
schema_version: 1.0
title: "Recurrent Networks & LSTM"
name: "Recurrent Neural Network / LSTM"
era: foundation
era_range: "1997–2016"
year: 1997
family: classical-dl
core_innovation: "A gated recurrent cell with an additive memory pathway that preserves gradients across long time steps."
description: "The gated recurrent architecture that made learning long-range sequence dependencies practical."
key_features: ["Recurrent hidden state", "Constant-error carousel memory cell", "Input/forget/output gates", "Backpropagation through time"]
key_models: ["LSTM (1997)", "GRU (2014)", "Seq2seq translation systems (2014–2016)"]
paper_title: "Long Short-Term Memory"
paper_url: "https://doi.org/10.1162/neco.1997.9.8.1735"
influenced_by: [architecture-era/mlp-backprop]
led_to: [architecture-era/seq2seq, architecture-era/mamba-ssm]
tags: [rnn, lstm, sequence, classical-dl, foundational]
updated: 2026-07-17
sources:
  - { url: "https://doi.org/10.1162/neco.1997.9.8.1735", title: "Long Short-Term Memory (Hochreiter & Schmidhuber, 1997, Neural Computation)", trust: 0.9 }
---

## What Changed
Feed-forward networks have no notion of order or memory; they process each input independently. A
recurrent network adds a hidden state that carries forward through time, so the network's output at
step $t$ depends on everything it has seen so far — the natural fit for language, speech, and time
series. But plain RNNs are almost untrainable over long spans: repeatedly multiplying gradients
through the recurrence makes them vanish or explode (Hochreiter's own diagnosis). The **LSTM** fixes
this with a dedicated memory cell whose state is updated *additively* rather than by repeated
multiplication — the "constant error carousel" — guarded by learned input, forget, and output gates
that decide what to write, keep, and read. This let recurrent nets remember information over hundreds
of steps and, for roughly two decades, made them the default for sequence modelling. The line runs
forward to encoder–decoder (seq2seq) translation, and its core idea — a selectively updated recurrent
state — resurfaces in modern linear-time state-space models like Mamba.

## Key Ideas
- **Recurrent state** — a hidden vector threads through the sequence, giving the network memory and
  making outputs depend on history.
- **Vanishing/exploding gradients** — backpropagation through time repeatedly multiplies Jacobians;
  without care the signal decays or blows up, capping how far back the network can learn.
- **Gated memory cell** — the LSTM's additive cell state plus forget/input/output gates create a
  protected gradient highway across time.
- **Sequential compute** — each step depends on the previous, so training cannot parallelise across
  the time axis; this bottleneck is precisely what the transformer later removed.
