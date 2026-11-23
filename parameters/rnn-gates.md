---
id: parameter/rnn-gates
type: parameter
slug: rnn-gates
schema_version: 2.9
title: "Gates (Gating Structure)"
name: num_gates
priority: 4
importance: important
applies_to: [rnn]
range: "1 — 4 weight blocks"
controls: "How many weight blocks a recurrent cell computes at each step: its gates plus the candidate state, each block reading the input and the previous state."
effects:
  decrease:
    - "Each block removed saves hidden × (input + hidden) weights plus its biases"
    - "Coarser control: the GRU has no output gate and exposes its whole state (Chung et al.)"
  increase:
    - "Each block added costs the same as the others: all blocks of a cell have the same shape"
    - "The LSTM's output gate controls how much of its memory the rest of the network sees (Chung et al.)"
scale:
  min: 1
  max: 4
  typical_min: 1
  typical_max: 4
  spacing: linear
typical_values:
  - setting: "PyTorch RNN: gate_size = hidden_size (rnn.py)"
    value: "1"
    source: "https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/rnn.py"
  - setting: "Cho et al.'s gated unit: reset gate, update gate and candidate"
    value: "3"
    source: "https://arxiv.org/abs/1406.1078"
  - setting: "PyTorch GRU: gate_size = 3 × hidden_size (rnn.py)"
    value: "3"
    source: "https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/rnn.py"
  - setting: "PyTorch LSTM: gate_size = 4 × hidden_size (rnn.py)"
    value: "4"
    source: "https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/rnn.py"
mistakes:
  - mistake: "Thinking some gates are bigger than others"
    why: >-
      Every block of a cell has the same shape: PyTorch stacks an LSTM's four blocks into one input
      weight of (4 × hidden) × input and one recurrent weight of (4 × hidden) × hidden. The bars of the
      figure are equal for this reason.
    fix: "Count one block and multiply by the number of blocks."
  - mistake: "Assuming PyTorch's GRU computes exactly Cho et al.'s unit"
    why: >-
      In Cho et al.'s Equation 8 the reset gate multiplies the previous state before the weight U. PyTorch's
      documentation says it applies r after W_hn h_{t−1} + b_hn, "on purpose for efficiency". The
      weights have the same shapes, so the count is the same; the computation is not.
    fix: "Expect identical counts but not identical outputs when moving weights between the two."
  - mistake: "Assuming each memory cell of the 1997 LSTM had its own gates"
    why: >-
      Hochreiter and Schmidhuber group S cells into a "memory cell block" that shares one input gate and
      one output gate, so a block of several cells needs only two extra gate units.
    fix: "Count gates per block for the 1997 networks."
  - mistake: "Assuming all LSTMs have the same weights per block"
    why: >-
      Gers et al. (2002) add peephole connections from the cell to its gates, which cost 3 weights per
      cell; Chung et al.'s LSTM has them as diagonal matrices. PyTorch's LSTM has none.
    fix: "Add 3 × hidden for a peephole LSTM."
related: [parameter/rnn-cell-type, parameter/rnn-hidden-units]
visual:
  kind: gate_blocks
  cells:
    - { label: "LSTM", blocks: ["Input gate", "Forget gate", "Cell gate (candidate)", "Output gate"] }
    - { label: "GRU", blocks: ["Reset gate", "Update gate", "New gate (candidate)"] }
  input_size: 500
  hidden_size: 1000
  biases: 0
  caption: "One layer of each cell split into its weight blocks (named as PyTorch's documentation names them, the candidate marked) at the sizes of Cho et al.'s encoder: 500-number word embeddings and 1,000 hidden units, biases left out as the paper's equations leave them out. Each block holds 500,000 input weights and 1,000,000 recurrent weights; the GRU's three blocks are the encoder's 4,500,000 weights."
tags: [rnn, lstm, gru, gates, memory, parameters]
updated: 2026-11-23
sources:
  - { url: "https://github.com/pytorch/pytorch/blob/main/torch/nn/modules/rnn.py", title: "PyTorch source: torch/nn/modules/rnn.py", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/stable/generated/torch.nn.LSTM.html", title: "PyTorch documentation: torch.nn.LSTM", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/stable/generated/torch.nn.GRU.html", title: "PyTorch documentation: torch.nn.GRU", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1406.1078", title: "Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation (Cho et al., arXiv:1406.1078)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1412.3555", title: "Empirical Evaluation of Gated Recurrent Neural Networks on Sequence Modeling (Chung, Gulcehre, Cho & Bengio, arXiv:1412.3555)", trust: 0.95 }
  - { url: "http://www.bioinf.jku.at/publications/older/2604.pdf", title: "Long Short-Term Memory (Hochreiter & Schmidhuber, Neural Computation 9(8), 1997)", trust: 0.95 }
  - { url: "https://www.jmlr.org/papers/volume3/gers02a/gers02a.pdf", title: "Learning Precise Timing with LSTM Recurrent Networks (Gers, Schraudolph & Schmidhuber, JMLR 3, 2002)", trust: 0.95 }
---

## Definition

**A gate is a set of numbers between 0 and 1** that multiplies another vector, letting part of it
through. Each gate, and each candidate state, is computed by its own weight block.

- **A weight block** multiplies the input x_t and the previous state h_{t−1} by its own weights,
  adds a bias, and applies a sigmoid (for a gate) or tanh (for a candidate).
- **The LSTM** has an input, a forget and an output gate plus the cell candidate: 4 blocks.
- **The GRU** has a reset and an update gate plus the candidate ("new" in PyTorch): 3 blocks.
- **There is no PyTorch setting for it:** the cell fixes it, and PyTorch's code sets gate_size to
  4, 3 or 1 times hidden_size.
- **The figure** splits one layer of each cell into its blocks.

**Analogy:** valves on pipes, each turned by its own hand, opening a little or a lot at every step.
The analogy shows the idea only; the counts are under Calculation.

## How It Works

**Every block reads the same two vectors; the gates then mix the results.**

1. **Compute the blocks** (PyTorch's LSTM): i_t, f_t, o_t = σ(W x_t + b + W h_{t−1} + b) with each
   gate's own weights, and the candidate g_t = tanh(…).
2. **Forget and write:** c_t = f_t ⊙ c_{t−1} + i_t ⊙ g_t, where ⊙ multiplies element by element.
3. **Read out:** h_t = o_t ⊙ tanh(c_t).
4. **GRU instead:** the reset gate r_t scales the old state inside the candidate n_t; the update gate
   z_t mixes: h_t = (1 − z_t) ⊙ n_t + z_t ⊙ h_{t−1}.

## Calculation

**Every block has the same count.**

In words: a block has hidden × input input weights, hidden × hidden recurrent weights and its bias
vectors; the layer has as many blocks as the cell computes.

$$
P_\text{block} = h\,x + h\,h + b\,h \qquad P_\text{layer} = G \times P_\text{block}
$$

| Symbol | Meaning |
| --- | --- |
| $G$ | weight blocks: 4 LSTM, 3 GRU, 1 plain RNN |
| $h$, $x$ | hidden_size, input_size |
| $b$ | bias vectors per block: 2 in PyTorch (b_ih, b_hh), 0 when weights only are counted |

For whole-network counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Cho et al.'s encoder:** 500-number word embeddings, 1,000 hidden units, biases left out as in the
paper's equations.

1. **Input weights of one block:** 1,000 × 500 = **500,000**.
2. **Recurrent weights of one block:** 1,000 × 1,000 = **1,000,000**.
3. **One block:** **1,500,000**.
4. **GRU, 3 blocks:** 3 × 1,500,000 = **4,500,000**, the encoder's recurrent layer.
5. **An LSTM of the same size, 4 blocks:** **6,000,000**.
6. **PyTorch's biases:** 2 × 1,000 = 2,000 per block; the GRU would hold **4,506,000**.
7. **A peephole LSTM** (Gers et al. 2002): 3 × 1,000 = 3,000 more weights.

**What this means:** gates are paid for block by block, all the same size; the LSTM's extra gate
costs one more block than the GRU.

## What To Watch

- **Naming.** "Gate" is used loosely; count blocks, not names.
- **Implementation details.** PyTorch's GRU places the reset gate differently from Cho et al.
- **Peepholes and projections.** They add weights outside the blocks.
