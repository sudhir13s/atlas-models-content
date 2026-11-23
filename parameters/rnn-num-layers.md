---
id: parameter/rnn-num-layers
type: parameter
slug: rnn-num-layers
schema_version: 3.1
title: Number of Layers (Stacked Depth)
name: num_layers
priority: 2
importance: critical
applies_to:
- rnn
range: 1 — 4
controls: How many recurrent layers are stacked, each reading the sequence of states of the layer below.
effects:
  decrease:
  - Fewer parameters by exactly the layers removed
  - 'A smaller total state: one layer of h and c instead of several'
  increase:
  - Each layer adds its own parameters; above the first, a layer reads the layer below's hidden values instead of the input
  - Sutskever et al. found deep LSTMs significantly outperformed shallow ones, each additional layer reducing perplexity by nearly 10%
  - Every layer runs at every step; Sutskever et al. put each of their 4 layers on its own GPU
scale:
  min: 1
  max: 8
  typical_min: 1
  typical_max: 4
  spacing: linear
typical_values:
- setting: PyTorch RNN, GRU and LSTM default (documentation)
  value: '1'
  source: https://docs.pytorch.org/docs/stable/generated/torch.nn.LSTM.html
- setting: 'Bahdanau et al., encoder of RNNsearch: one bidirectional layer'
  value: '1'
  source: https://arxiv.org/abs/1409.0473
- setting: Sutskever et al., encoder and decoder LSTMs
  value: '4'
  source: https://arxiv.org/abs/1409.3215
mistakes:
- mistake: Assuming every layer of a stack has the same count
  why: PyTorch sizes weight_ih_l0 as (blocks × hidden) × input_size, but every higher layer as (blocks × hidden) × (directions × hidden). A GRU with 620-number inputs and 1,000 units has **4,860,000** weights in layer 1 and **6,000,000** in layer 2 (biases left out).
  fix: Count the first layer from input_size and the others from hidden_size.
- mistake: Reading the first dimension of h_n as the number of layers
  why: PyTorch's h_0 and h_n have a first dimension of D × num_layers, where D is 2 for a bidirectional network and 1 otherwise.
  fix: Divide by 2 when the network is bidirectional.
- mistake: Expecting dropout after every layer
  why: PyTorch applies the dropout argument to the outputs of each layer except the last, so with num_layers = 1 it has nothing to act on.
  fix: Use dropout only with two or more layers, or add it outside the recurrent module.
related:
- parameter/rnn-hidden-units
- parameter/rnn-bidirectional
- parameter/rnn-cell-type
visual:
  kind: recurrent_layers
  stacks:
  - label: Encoder
    gates: 4
    input_size: 1000
    hidden_size: 1000
    num_layers: 4
    bidirectional: false
  biases: 0
  caption: 'Weights of each layer of Sutskever et al.''s encoder LSTM: 4 layers of 1,000 cells reading 1,000-number word embeddings. Because the input is as wide as the state, every layer has the same 8,000,000 weights; together 32,000,000, the paper''s 32M. Biases are left out, as in the paper''s count.'
tags:
- rnn
- lstm
- recurrent
- depth
- structure
- parameters
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/1409.3215
  title: Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals & Le, arXiv:1409.3215)
  trust: 0.95
- url: https://docs.pytorch.org/docs/stable/generated/torch.nn.LSTM.html
  title: 'PyTorch documentation: torch.nn.LSTM'
  trust: 0.9
- url: https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/rnn.py
  title: 'PyTorch source: torch/nn/modules/rnn.py'
  trust: 0.9
- url: https://arxiv.org/abs/1409.0473
  title: Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho & Bengio, arXiv:1409.0473)
  trust: 0.95
---

## Definition

**num_layers is how many recurrent layers are stacked.** PyTorch's documentation: "setting
num_layers=2 would mean stacking two LSTMs together", the second taking in the outputs of the first.

- **Layer 1** reads the input sequence, one vector per step.
- **Each higher layer** reads the sequence of states h of the layer below.
- **Every layer** has its own weights and its own state.
- **The figure** counts the 4 layers of Sutskever et al.'s encoder.

**Analogy:** several readers in a row, each reading the notes of the one before rather than the
book itself. The analogy shows the idea only; the counts are under Calculation.

## How It Works

**At each step, the input climbs the stack.**

1. **Layer 1** combines x_t with its own previous state into h_t^(1).
2. **Layer 2** treats h_t^(1) as its input and combines it with its own previous state.
3. **Upwards** to layer L, whose states are the stack's output.
4. **State.** Each layer keeps its own h (and c in an LSTM): Sutskever et al.'s 4 layers of 1,000
   cells hold 8,000 numbers.

## Calculation

**The first layer reads the input; the others read hidden values.**

In words: add one count per layer; the first uses input_size, every other uses directions ×
hidden_size as its input.

$$
P = G\,h\,(x + h) + (L - 1)\,G\,h\,(D h + h) \quad (+\ \text{biases})
$$

| Symbol | Meaning |
| --- | --- |
| $L$ | num_layers |
| $h$, $x$ | hidden_size, input_size |
| $G$ | weight blocks: 4 LSTM, 3 GRU, 1 plain RNN |
| $D$ | directions: 1, or 2 if bidirectional (then each layer's count doubles too) |

PyTorch adds 2 × G × h biases per layer and direction. For whole-network counts, see the
[parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Sutskever et al.'s translation model:** two LSTMs of 4 layers, 1,000 cells, 1,000-number word
embeddings, vocabularies of 160,000 and 80,000 words.

1. **Layer 1:** 4 × 1,000 × (1,000 + 1,000) = **8,000,000**.
2. **Layers 2 to 4:** they read 1,000 hidden values, so the same **8,000,000** each.
3. **Encoder:** 4 × 8,000,000 = **32,000,000**, the paper's 32M; the decoder has as many.
4. **Both LSTMs:** **64,000,000**, the paper's "64M are pure recurrent connections".
5. **The rest of its 384M:** 160,000 × 1,000 + 80,000 × 1,000 + 80,000 × 1,000 = **320,000,000**,
   consistent with the two embedding tables and the 80,000-word softmax (the paper gives only the
   totals).
6. **A narrower input, for contrast:** a GRU reading 620 numbers with 1,000 units has 4,860,000
   weights in layer 1 but 6,000,000 in layer 2; an example, not a published network.

**What this means:** each layer adds a full layer's weights; the first differs from the rest only
when the input is narrower or wider than the state.

## What To Watch

- **bidirectional.** It doubles every layer and widens what the layers above read.
- **dropout.** Applied between layers only.
- **Embedding and output layers.** In Sutskever et al.'s model they hold most of the parameters.
