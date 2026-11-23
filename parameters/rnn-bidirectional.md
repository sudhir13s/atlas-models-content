---
id: parameter/rnn-bidirectional
type: parameter
slug: rnn-bidirectional
schema_version: 2.9
title: "Bidirectional"
name: bidirectional
priority: 5
importance: important
applies_to: [rnn]
range: "false or true"
controls: "Whether each layer runs a second recurrence from the end of the sequence back to the start, so every position sees what comes after it as well as before."
options:
  - value: "false"
    effect: "One direction: each output depends only on the steps before it. PyTorch's D is 1."
  - value: "true"
    effect: "A forward and a backward recurrence with their own weights; their outputs are joined, 2 × hidden numbers per step. PyTorch's D is 2."
typical_values:
  - setting: "PyTorch RNN, GRU and LSTM default (documentation)"
    value: "false"
    source: "https://docs.pytorch.org/docs/stable/generated/torch.nn.LSTM.html"
  - setting: "Sutskever et al.: one direction, with the source sentence fed in reverse"
    value: "false"
    source: "https://arxiv.org/abs/1409.3215"
  - setting: "Bahdanau et al., RNNsearch encoder: forward and backward RNNs of 1,000 units each"
    value: "true"
    source: "https://arxiv.org/abs/1409.0473"
  - setting: "Schuster and Paliwal, BRNN for TIMIT phoneme classification"
    value: "true"
    source: "https://ieeexplore.ieee.org/document/650093"
mistakes:
  - mistake: "Assuming bidirectional exactly doubles the parameters"
    why: >-
      It doubles each layer at the same hidden_size, but a higher layer then reads 2 × hidden numbers
      (PyTorch sizes weight_ih_l[k] by num_directions × hidden_size). In the figure's two-layer GRU the
      total goes from **10,860,000** to **27,720,000**, 2.55 times. Whatever reads the output grows too:
      Bahdanau et al.'s decoder matrices C, C_z, C_r are n × 2n.
    fix: "Count each layer from what it reads, as in the figure."
  - mistake: "Thinking a bidirectional network must be twice as large to be worth it"
    why: >-
      Schuster and Paliwal split the state neurons of one network into a forward and a backward part and
      compared structures with about the same number of free parameters, about 13,000. Their BRNN
      reached **68.53%** on TIMIT phonemes, against **63.2%** for a forward RNN and **65.28%** for two
      separate one-way networks merged.
    fix: "Compare one-way and two-way networks at equal parameter counts, as the paper did."
  - mistake: "Using a bidirectional layer to generate a sequence step by step"
    why: >-
      The backward direction starts from the last step, so it needs the whole input first. Sutskever et
      al. kept one direction and reversed the source sentence instead; reversing raised their test BLEU
      score from 25.9 to 30.6.
    fix: "Use bidirectional layers for inputs that are complete before processing, such as an encoder's."
related: [parameter/rnn-num-layers, parameter/rnn-hidden-units, parameter/rnn-cell-type]
visual:
  kind: recurrent_layers
  stacks:
    - { label: "One-way", gates: 3, input_size: 620, hidden_size: 1000, num_layers: 2, bidirectional: false }
    - { label: "Two-way", gates: 3, input_size: 620, hidden_size: 1000, num_layers: 2, bidirectional: true }
  biases: 0
  caption: "Weights of each layer of a GRU stack with Bahdanau et al.'s encoder sizes (620-number embeddings, 1,000 units per direction), biases left out as in the paper's equations. Layer 1 is their encoder: 4,860,000 one way, 9,720,000 both ways. Layer 2 is an example added to show what the next layer reads: 1,000 numbers one way, 2,000 both ways, so it triples."
tags: [rnn, lstm, bidirectional, context, parameters]
updated: 2026-11-23
sources:
  - { url: "https://ieeexplore.ieee.org/document/650093", title: "Bidirectional Recurrent Neural Networks (Schuster & Paliwal, IEEE Transactions on Signal Processing 45(11), 1997; doi:10.1109/78.650093)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1409.0473", title: "Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho & Bengio, arXiv:1409.0473)", trust: 0.95 }
  - { url: "https://docs.pytorch.org/docs/stable/generated/torch.nn.LSTM.html", title: "PyTorch documentation: torch.nn.LSTM", trust: 0.9 }
  - { url: "https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/rnn.py", title: "PyTorch source: torch/nn/modules/rnn.py", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1409.3215", title: "Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals & Le, arXiv:1409.3215)", trust: 0.95 }
---

## Definition

**bidirectional runs each recurrent layer twice: once from the first step to the last and once
from the last to the first.** PyTorch's documentation: "If True, becomes a bidirectional LSTM."

- **Forward states** summarise everything up to a position; **backward states** everything after it.
- **Joined:** at each step the two are put side by side, 2 × hidden numbers (Bahdanau et al. call
  this the annotation of a word).
- **Separate weights:** the two directions do not share recurrent weights.
- **The figure** counts a one-way and a two-way stack layer by layer.

**Analogy:** reading a sentence once left to right and once right to left, and noting for each word
what came before and what comes after. The analogy shows the idea only.

## How It Works

**Two passes, then join.**

1. **Forward pass.** One recurrence reads x_1 to x_T and gives forward states.
2. **Backward pass.** A second recurrence, with its own weights, reads x_T to x_1.
3. **Join.** At each position the forward and backward states are concatenated; Schuster and
   Paliwal note that outputs of the forward states are not connected to the inputs of the backward
   states, and vice versa.
4. **Stack.** A layer above reads these joined states, 2 × hidden numbers per step.
5. **Shared parts.** Bahdanau et al. share the word embedding between the directions, but not the
   recurrent weights.

## Calculation

**Two directions per layer; wider inputs above the first.**

In words: each layer counts both directions; the first reads the input, the others read the
joined output of the layer below.

$$
P_1 = 2\,G\,h\,(x + h) \qquad P_{\ell \ge 2} = 2\,G\,h\,(2h + h) \quad (+\ \text{biases})
$$

| Symbol | Meaning |
| --- | --- |
| $G$ | weight blocks: 4 LSTM, 3 GRU, 1 plain RNN |
| $h$, $x$ | hidden_size per direction, input_size |
| $2h$ | what a layer above reads: both directions joined |

PyTorch adds 2 × G × h biases per layer and direction. For whole-network counts, see the
[parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Bahdanau et al.'s encoder:** gated units as in the cell-type page, 620-number embeddings, 1,000
units per direction, biases left out as in the paper's equations.

1. **Forward direction:** 3 × 1,000 × (620 + 1,000) = **4,860,000**.
2. **Backward direction:** the same shapes, **4,860,000**.
3. **The encoder:** **9,720,000**, twice one direction.
4. **What the decoder reads:** 2,000-number annotations, so C, C_z, C_r of 1,000 × 2,000 are
   3 × 2,000,000 = **6,000,000**, against 3,000,000 if they read 1,000.
5. **A second layer** (an example): one way 3 × 1,000 × (1,000 + 1,000) = 6,000,000; both ways
   2 × 3 × 1,000 × (2,000 + 1,000) = **18,000,000**.
6. **Two layers in all:** one way 10,860,000, both ways **27,720,000**, 2.55 times.

**What this means:** bidirectional doubles a layer, and more than doubles a stack and whatever reads
it; it fits inputs that are complete before they are processed.

## What To Watch

- **Output width.** 2 × hidden per step; resize the layers that read it.
- **h_n.** Its first dimension is 2 × num_layers in PyTorch.
- **Causal use.** A backward pass needs the whole input; streaming or step-by-step generation cannot
  use it.
