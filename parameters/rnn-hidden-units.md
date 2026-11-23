---
id: parameter/rnn-hidden-units
type: parameter
slug: rnn-hidden-units
schema_version: 3.1
title: Hidden Units (hidden_size)
name: hidden_size
priority: 1
importance: critical
applies_to:
- rnn
range: 6 — 1,000
controls: 'How many numbers the recurrent state holds: the size of the vector each layer carries from one step of the sequence to the next.'
effects:
  decrease:
  - 'Fewer parameters: each weight block holds hidden × (input + hidden) weights'
  - A smaller state to carry information from step to step
  - At equal parameter budgets, Chung et al. used 36 LSTM units against 100 plain tanh units
  increase:
  - 'Parameters grow faster than in a straight line because of the hidden × hidden part: for an LSTM layer reading 1,000 numbers, 8,000,000 weights at 1,000 units, 24,000,000 at 2,000'
  - 'A larger state: Sutskever et al.''s 4 layers of 1,000 cells represent a sentence with 8,000 numbers'
scale:
  min: 4
  max: 4096
  typical_min: 6
  typical_max: 1000
  spacing: log
typical_values:
- setting: 'LSTM 1997, Experiment 1: 3 memory cell blocks of 2 cells (Hochreiter and Schmidhuber)'
  value: '6'
  source: http://www.bioinf.jku.at/publications/older/2604.pdf
- setting: Chung et al., LSTM for music modelling, about 19,800 parameters
  value: '36'
  source: https://arxiv.org/abs/1412.3555
- setting: Chung et al., LSTM for speech signal modelling, about 169,100 parameters
  value: '195'
  source: https://arxiv.org/abs/1412.3555
- setting: Cho et al., encoder of the RNN Encoder–Decoder (gated units)
  value: '1000'
  source: https://arxiv.org/abs/1406.1078
- setting: Sutskever et al., each of 4 LSTM layers
  value: '1000'
  source: https://arxiv.org/abs/1409.3215
mistakes:
- mistake: Assuming that doubling hidden_size quadruples the layer
  why: 'Only the hidden × hidden part grows four-fold; the hidden × input part doubles. For an LSTM layer reading 1,000 numbers, 1,000 units have **8,000,000** weights and 2,000 units **24,000,000**: three times, not four.'
  fix: Compute blocks × hidden × (input + hidden), as in the figure.
- mistake: Comparing cells at the same hidden_size as if they cost the same
  why: 'Chung et al. matched parameters, not units: about 20,000 parameters meant **36** LSTM units, **46** GRU units or **100** tanh units for music; about 169,000 meant 195, 227 and 400 for speech.'
  fix: When comparing cells, compare parameter counts as well as hidden sizes.
- mistake: Expecting a paper's count to match PyTorch's
  why: 'PyTorch stores two bias vectors per layer, b_ih and b_hh; its source says the second is "included for CuDNN compatibility" and only one is needed. Papers often leave biases out: one of Sutskever et al.''s layers is 8,000,000 weights, while PyTorch''s LSTM of the same size holds **8,008,000**.'
  fix: Say which bias convention a count uses.
- mistake: Reading the 1997 LSTM's hidden units as cells with their own gates
  why: In the 1997 paper, S memory cells form a "memory cell block" that shares one input gate and one output gate. In PyTorch's LSTM every one of the hidden_size units has its own gates.
  fix: Count gates per block for the 1997 networks, per unit for PyTorch.
related:
- parameter/rnn-cell-type
- parameter/rnn-num-layers
- parameter/rnn-gates
- parameter/rnn-bidirectional
visual:
  kind: recurrent_sweep
  cells:
  - label: LSTM
    gates: 4
  input_size: 1000
  hidden_sizes:
  - 250
  - 500
  - 750
  - 1000
  - 1500
  - 2000
  marked: 1000
  biases: 0
  caption: 'Weights of one LSTM layer reading 1,000 numbers per step, as hidden_size changes; marked: the 1,000 cells of each layer of Sutskever et al., which also used 1,000-number word embeddings. Biases are left out, as the paper''s count of 32M weights for 4 layers leaves them out.'
tags:
- rnn
- lstm
- recurrent
- hidden-state
- width
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
- url: https://arxiv.org/abs/1412.3555
  title: Empirical Evaluation of Gated Recurrent Neural Networks on Sequence Modeling (Chung, Gulcehre, Cho & Bengio, arXiv:1412.3555)
  trust: 0.95
- url: https://arxiv.org/abs/1406.1078
  title: Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation (Cho et al., arXiv:1406.1078)
  trust: 0.95
- url: http://www.bioinf.jku.at/publications/older/2604.pdf
  title: Long Short-Term Memory (Hochreiter & Schmidhuber, Neural Computation 9(8), 1997)
  trust: 0.95
---

## Definition

**hidden_size is the number of values in a recurrent layer's state.** PyTorch's documentation
calls it "the number of features in the hidden state h".

- **A recurrent layer** reads a sequence one step at a time and carries a state vector forward.
- **The state** is the layer's memory of what it has read; hidden_size is its length.
- **In an LSTM** there are two such vectors of this size, the output h and the cell state c.
- **The figure** shows how one LSTM layer's weights grow with hidden_size.

**Analogy:** the size of a notepad carried from page to page of a book. The analogy shows the idea
only; the exact counts are under Calculation.

## How It Works

**At every step, input and previous state go through the cell's weight blocks.**

1. **Read.** The input at step t, x_t, and the previous state h_{t−1} arrive together.
2. **Weight blocks.** Each block multiplies x_t by an input weight (hidden × input) and h_{t−1} by a
   recurrent weight (hidden × hidden): an LSTM has 4 blocks, a GRU 3, a plain RNN 1.
3. **Update.** The cell combines the blocks into the new state h_t of hidden_size values.
4. **Carry.** h_t goes to the next step and, in a stack, to the layer above.

## Calculation

**One layer's parameters, in one direction.**

In words: each block has hidden × input input weights and hidden × hidden recurrent weights, plus
one or two bias vectors of length hidden.

$$
P = G\,h\,(x + h) + b\,G\,h
$$

| Symbol | Meaning |
| --- | --- |
| $h$ | hidden_size |
| $x$ | input_size |
| $G$ | weight blocks: 4 LSTM, 3 GRU, 1 plain RNN |
| $b$ | bias vectors per block: 2 in PyTorch, 0 when a paper counts weights only |

The $G h^2$ part grows with the square of $h$. For whole-network counts, see the
[parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**One layer of Sutskever et al.'s translation LSTM:** 1,000 cells reading 1,000-number word
embeddings.

1. **Input weights:** 4 × 1,000 × 1,000 = **4,000,000**.
2. **Recurrent weights:** 4 × 1,000 × 1,000 = **4,000,000**.
3. **One layer:** 4 × 1,000 × (1,000 + 1,000) = **8,000,000**.
4. **Four layers:** 4 × 8,000,000 = **32,000,000**, the paper's "32M for the encoder LSTM".
5. **With PyTorch's two bias vectors:** 2 × 4 × 1,000 = 8,000 more per layer, **8,008,000**.
6. **At 2,000 cells:** 4 × 2,000 × (1,000 + 2,000) = **24,000,000**, three times as many.
7. **The state:** 4 layers × (1,000 + 1,000) for h and c = **8,000** numbers, the paper's figure.

**What this means:** hidden_size sets the largest part of a recurrent layer, and its square term
dominates once it exceeds the input size.

## What To Watch

- **input_size.** It sets the other half of the count; above the first layer it equals hidden_size
  (twice that when bidirectional).
- **Bias convention.** PyTorch counts two bias vectors; many papers none.
- **proj_size.** A PyTorch LSTM with proj_size above 0 shrinks h and adds a projection matrix.
