---
id: parameter/rnn-cell-type
type: parameter
slug: rnn-cell-type
schema_version: 3.1
title: Cell Type (RNN / GRU / LSTM)
name: rnn_type
priority: 3
importance: critical
applies_to:
- rnn
range: RNN, GRU or LSTM
controls: 'Which recurrent unit every layer uses: a plain RNN with one weight block, a GRU with three, or an LSTM with four.'
options:
- value: RNN (tanh)
  effect: One weight block; the state is replaced by a new weighted sum at every step (Chung et al.).
- value: GRU
  effect: 'Three blocks: reset gate, update gate and candidate. No separate memory cell; the whole state is exposed at every step (Chung et al.).'
- value: LSTM
  effect: 'Four blocks: input, forget and output gates and the cell candidate. A separate memory cell, read out through the output gate.'
typical_values:
- setting: Chung et al., the plain baseline of their comparison
  value: tanh RNN
  source: https://arxiv.org/abs/1412.3555
- setting: 'Cho et al., RNN Encoder–Decoder: the gated unit later called the GRU'
  value: GRU
  source: https://arxiv.org/abs/1406.1078
- setting: Bahdanau et al., RNNsearch encoder and decoder (gated units as Cho et al.)
  value: GRU
  source: https://arxiv.org/abs/1409.0473
- setting: Sutskever et al., translation encoder and decoder
  value: LSTM
  source: https://arxiv.org/abs/1409.3215
mistakes:
- mistake: Counting the GRU's gates as three and the LSTM's as four
  why: 'Authors count differently. Cho et al. describe their unit as having **two** gating units, reset and update; the third block computes the candidate state. PyTorch''s documentation names an LSTM''s four parts the input, forget, cell and output gates, and a GRU''s three the reset, update and new gates. What is fixed is the number of weight blocks: **4, 3 and 1**.'
  fix: Count weight blocks when computing parameters, whatever the parts are called.
- mistake: Assuming one gated cell is simply better
  why: 'Chung et al. compared LSTM and GRU at about the same parameter count on music and speech. Both clearly beat the tanh unit, but they could not make a concrete conclusion on which of the two is better: it may depend on the dataset and task.'
  fix: Compare cells on the task at hand, at equal parameter counts.
- mistake: Comparing cells at the same hidden size as if they cost the same
  why: At equal hidden and input sizes an LSTM layer has **4/3** of a GRU's weights and **4** times a plain RNN's. To match about 20,000 parameters Chung et al. used **36** LSTM units, **46** GRU units and **100** tanh units.
  fix: Match parameter counts, not hidden sizes, when comparing.
- mistake: Taking today's LSTM for the 1997 one
  why: The 1997 cell had input and output gates only; Gers et al. (2002) write that they build on LSTM with forget gates (Gers, Schmidhuber and Cummins, 2000) and add peephole connections, 3 weights per cell. PyTorch's LSTM equations have a forget gate and no peepholes.
  fix: Name the variant when quoting a count or a result.
related:
- parameter/rnn-gates
- parameter/rnn-hidden-units
- parameter/rnn-num-layers
visual:
  kind: recurrent_sweep
  cells:
  - label: LSTM (4 blocks)
    gates: 4
  - label: GRU (3 blocks)
    gates: 3
  - label: RNN (1 block)
    gates: 1
  input_size: 620
  hidden_sizes:
  - 250
  - 500
  - 750
  - 1000
  - 1500
  - 2000
  marked: 1000
  biases: 0
  caption: 'Weights of one layer of each cell reading 620-number word embeddings, as hidden_size changes; marked: the 1,000 units of Bahdanau et al.''s encoder, whose GRU-type layer has the 4,860,000 weights of the middle line. Biases are left out, as the paper''s equations leave them out; the three lines keep the ratio 4 : 3 : 1 at every size.'
tags:
- rnn
- lstm
- gru
- gates
- cell
- parameters
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/1412.3555
  title: Empirical Evaluation of Gated Recurrent Neural Networks on Sequence Modeling (Chung, Gulcehre, Cho & Bengio, arXiv:1412.3555)
  trust: 0.95
- url: https://arxiv.org/abs/1406.1078
  title: Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation (Cho et al., arXiv:1406.1078)
  trust: 0.95
- url: https://arxiv.org/abs/1409.0473
  title: Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho & Bengio, arXiv:1409.0473)
  trust: 0.95
- url: https://arxiv.org/abs/1409.3215
  title: Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals & Le, arXiv:1409.3215)
  trust: 0.95
- url: https://docs.pytorch.org/docs/stable/generated/torch.nn.LSTM.html
  title: 'PyTorch documentation: torch.nn.LSTM'
  trust: 0.9
- url: https://docs.pytorch.org/docs/stable/generated/torch.nn.GRU.html
  title: 'PyTorch documentation: torch.nn.GRU'
  trust: 0.9
- url: https://docs.pytorch.org/docs/stable/generated/torch.nn.RNN.html
  title: 'PyTorch documentation: torch.nn.RNN'
  trust: 0.9
- url: http://www.bioinf.jku.at/publications/older/2604.pdf
  title: Long Short-Term Memory (Hochreiter & Schmidhuber, Neural Computation 9(8), 1997)
  trust: 0.95
- url: https://www.jmlr.org/papers/volume3/gers02a/gers02a.pdf
  title: Learning Precise Timing with LSTM Recurrent Networks (Gers, Schraudolph & Schmidhuber, JMLR 3, 2002)
  trust: 0.95
---

## Definition

**The cell type is the recurrent unit every layer uses.** In PyTorch it is the module chosen:
nn.RNN, nn.GRU or nn.LSTM.

- **Plain RNN:** h_t = tanh(W x_t + U h_{t−1} + b), one weight block.
- **GRU** (gated recurrent unit, Cho et al. 2014): a reset gate and an update gate decide how much
  of the old state to use and to keep; three blocks.
- **LSTM** (long short-term memory): a separate memory cell with input, forget and output gates;
  four blocks.
- **The figure** compares one layer of each at the same sizes.

**Analogy:** three notebooks: one rewritten from scratch each page, one where you choose how much to
overwrite, one with a separate locked drawer you choose to open. The analogy shows the idea only.

## How It Works

**Same inputs, different numbers of blocks and different ways to update.**

1. **Plain RNN.** One block computes the new state; the old one is replaced (Chung et al.).
2. **GRU.** The reset gate r scales the old state inside the candidate; the update gate z mixes old
   state and candidate. Chung et al. note it exposes its whole state each time.
3. **LSTM.** The cell state c is kept, scaled by the forget gate and added to through the input gate;
   the output gate decides how much of it is shown as h.
4. **What they share.** Chung et al. point to the additive update of both gated units, which lets
   information and error pass many steps.

## Calculation

**The cell sets the number of weight blocks G.**

In words: every block has the same shape, so the cell multiplies one block's count.

$$
P = G \left[ h\,(x + h) + b\,h \right] \qquad G_\text{LSTM} = 4,\ G_\text{GRU} = 3,\ G_\text{RNN} = 1
$$

| Symbol | Meaning |
| --- | --- |
| $G$ | weight blocks of the cell |
| $h$, $x$ | hidden_size, input_size |
| $b$ | bias vectors per block: 2 in PyTorch, 0 when weights only are counted |

PyTorch sets each layer's gate_size to 4, 3 or 1 times hidden_size. For whole-network counts, see
the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Bahdanau et al.'s encoder sizes:** 620-number word embeddings, 1,000 hidden units, one direction,
biases left out as in the paper's equations.

1. **One block:** 1,000 × (620 + 1,000) = **1,620,000**.
2. **Plain RNN:** 1 block, **1,620,000**.
3. **GRU:** 3 blocks, **4,860,000**: the forward encoder of RNNsearch (W, W_z, W_r of 1,000 × 620 and
   U, U_z, U_r of 1,000 × 1,000).
4. **LSTM:** 4 blocks, **6,480,000**.
5. **Ratios:** the LSTM is 4/3 ≈ 1.33 times the GRU and 4 times the plain RNN at every size.
6. **Chung et al., the other way round:** at about 20,000 parameters they used 36 LSTM units against
   100 tanh units.

**What this means:** the cell multiplies the layer's size by 1, 3 or 4; which gated cell works
better was, in Chung et al.'s tests, a matter of task.

## What To Watch

- **Variant.** Forget gate, peepholes and GRU reset placement differ between papers and PyTorch.
- **Bias convention.** PyTorch's two bias vectors per block add 2 × G × hidden.
- **Equal budgets.** Compare cells at matched parameter counts.
