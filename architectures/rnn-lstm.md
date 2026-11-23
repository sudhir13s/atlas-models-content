---
id: architecture-era/rnn-lstm
type: architecture-era
slug: rnn-lstm
schema_version: 3.1
title: Recurrent Networks & LSTM
name: Recurrent Neural Network / LSTM
era: foundation
era_range: 1997–2016
year: 1997
family: classical-dl
core_innovation: A gated recurrent cell with an additive memory pathway that preserves gradients across long time steps.
description: A neural network that reads a sequence one step at a time and carries a memory from step to step; the LSTM adds a gated memory cell so that this memory can last over long stretches of the sequence.
key_features:
- Recurrent hidden state
- LSTM memory cell with a constant error carousel
- Multiplicative gates controlling the memory cell
- Backpropagation through time
paper_title: Long Short-Term Memory
paper_url: https://doi.org/10.1162/neco.1997.9.8.1735
influenced_by:
- architecture-era/mlp-backprop
led_to:
- architecture-era/seq2seq
- architecture-era/mamba-ssm
analogy:
  title: Reading a mystery novel with a notebook
  body: This is an analogy, not the mechanism. A plain recurrent network is a reader who keeps the whole plot in their head and rewrites that memory on every page, so a clue from chapter two has faded by chapter twenty. An LSTM reader keeps a notebook (the memory cell). At each page they decide whether to write a new note (the input gate), whether to glance at the notebook right now (the output gate) and, in the version with a forget gate added in 2000, whether to cross old notes out. Because the notebook is only added to or crossed out, never rewritten from scratch, a clue can survive to the last page. The exact rule is the key equation below.
narrative:
  motivation: '**Plain recurrent networks could not learn to remember for long.** The LSTM paper is by **Hochreiter and Schmidhuber (1997)**.


    - **What a recurrent network does:** it feeds its own state back in at the next step, so it can in principle keep a short-term memory of recent inputs.

    - **The problem:** with the usual training methods, backpropagation through time (**BPTT**) and real-time recurrent learning (**RTRL**), error signals sent backwards in time tend to **blow up or vanish**.

    - **Why:** the authors review **Hochreiter''s 1991 analysis**: the backpropagated error depends **exponentially** on the size of the weights.

    - **The result:** in their tests, BPTT and RTRL already fail when the gap between an input and the target that depends on it is **10 steps**.'
  approach: '**Make one path where the error is never scaled.**


    - **The constant error carousel (CEC):** a unit that feeds back into itself with weight **1.0** and a linear activation keeps its value, and the error flowing back through it stays **constant**.

    - **Two conflicts:** the same input weights would have to both store and ignore inputs, and the same output weights would have to both read the memory and stay silent.

    - **The fix:** put **multiplicative gate units** in front of and behind the carousel, which learn when to open and close access to it.'
  solution: '**A memory cell guarded by gates.**


    - **1997 cell:** the internal state is updated as **s(t) = s(t−1) + y_in(t) · g(net(t))**: the old value is kept and a gated new value is added.

    - **Input gate:** protects the stored value from irrelevant inputs.

    - **Output gate:** protects the rest of the network from a stored value that is not relevant yet.

    - **Forget gate:** **Gers, Schmidhuber and Cummins (2000)** added a third gate that multiplies the old state, so the cell can learn to **reset** itself; Gers et al. (2002) write that this keeps the state from **growing to infinity** and lets the cell store fresh data without interference from what came before.'
  outcome: '**Long time lags, then machine translation.**


    - **In the 1997 paper:** LSTM learns tasks with minimal time lags of **1,000 steps**, including noisy ones, that the authors report no earlier recurrent network algorithm had solved.

    - **Translation:** **Sutskever, Vinyals and Le (2014)** used a **4-layer** LSTM to read an English sentence and another to write the French one, reaching **34.8 BLEU** on WMT 2014 English to French against **33.3** for a phrase-based system.

    - **Simpler gating:** **Cho et al. (2014)** proposed a gated unit motivated by the LSTM but simpler to compute, later called the **GRU**.

    - **What replaced it:** the **Transformer (2017)** dispenses with recurrence; **Mamba (2023)** returns to a recurrent state that can selectively propagate or forget information.'
key_equation:
  latex: c_t = f_t \odot c_{t-1} + i_t \odot g_t
  plain: 'In words: the new memory is the old memory scaled by the forget gate, plus the new candidate value scaled by the input gate. Nothing squashes the old memory on the way through, so a value, and the error signal that trains it, can pass many steps almost unchanged when the forget gate stays near 1. The 1997 cell has no forget gate, which is the same as fₜ = 1. The worked example above runs this update for three steps.'
  terms:
  - symbol: c_t
    meaning: the cell state (memory) after step t; the 1997 paper calls it the internal state s
  - symbol: f_t
    meaning: the forget gate, a value between 0 and 1 from a logistic sigmoid; added by Gers, Schmidhuber and Cummins (2000)
  - symbol: i_t
    meaning: 'the input gate, a value between 0 and 1: how much of the new candidate is written'
  - symbol: g_t
    meaning: the candidate value computed from the current input and the previous output (tanh in the form Chung et al. 2014 use)
  - symbol: ⊙
    meaning: 'element-wise product: each number of one vector times the matching number of the other'
strengths:
- '**Long time lags:** the authors report learning tasks with minimal time lags of **1,000 steps**, with noise, distributed representations and continuous values.'
- '**Cheap per step:** the update cost per weight and time step is **O(1)**, as for BPTT, and the method is local in space and time, so it does not store past activations in a stack of unlimited size.'
- '**Little tuning:** the authors report that LSTM works over a **broad range** of learning rates and gate biases.'
- '**Gates beat plain units:** **Chung et al. (2014)** report that both gated units, LSTM and GRU, **clearly outperformed** a traditional tanh unit, most of all on raw speech signal modelling.'
- '**Sequences of any length:** one set of weights is applied at every step, so the same network reads a sentence of 5 words or of 50.'
weaknesses:
- '**One step at a time:** the Transformer authors note that recurrence is **inherently sequential**, which **precludes parallelization** within training examples.'
- '**One vector for a whole sentence:** **Bahdanau et al. (2014)** conjecture that encoding a source sentence into a **fixed-length vector** is a bottleneck, and cite Cho et al. showing that a basic encoder–decoder **deteriorates rapidly** as sentences get longer.'
- '**More units and weights:** each memory cell block needs **two extra gate units**; the 1997 authors put the growth in weights at **at most a factor of 9** in the fully connected case.'
- '**Exploding gradients remain:** Sutskever et al. write that LSTMs tend not to suffer from vanishing gradients but **can have exploding ones**, and rescaled any gradient whose norm exceeded **5**.'
- '**Tasks it does not solve:** the 1997 authors list **strongly delayed XOR** problems and **counting exact time steps** (telling 99 from 100 steps ago) as beyond the truncated algorithm.'
visual:
  kind: recurrent_memory
  plain_weight: 0.5
  inputs:
  - 2
  - 0
  - 0
  gates:
    forget:
    - 0
    - 4
    - 4
    input:
    - 3
    - -3
    - -3
    candidate:
    - 2
    - -1
    - 0
    output:
    - 0
    - -2
    - 3
  horizon: 10
  caption: 'Computed from the worked example''s inputs: the plain network hₜ = tanh(0.5 × hₜ₋₁ + xₜ) with x = 2, 0, 0, and one LSTM cell from the gate sums of the three steps. The last panel follows the error further back on a log scale: the plain network continues with empty steps (x = 0), and the LSTM repeats its last forget gate σ(4) ≈ 0.982, as the example does for 10 steps. The numbers are an example, not from a trained model.'
comparison:
  before: Plain recurrent network (BPTT or RTRL)
  after: LSTM (1997)
  rows:
  - aspect: How the stored value changes at each step
    before: 'Recomputed: each unit squashes a new weighted sum of the previous step''s activations'
    after: 'Kept and added to: s(t) = s(t−1) + y_in(t) · g(net(t))'
    source: http://www.bioinf.jku.at/publications/older/2604.pdf
  - aspect: What happens to an error sent q steps back
    before: Multiplied by q factors f′ · w; with logistic units and weights below 4.0 each factor is below 1, so it vanishes
    after: Inside the constant error carousel it flows back without being scaled
    source: http://www.bioinf.jku.at/publications/older/2604.pdf
  - aspect: Longest minimal time lag learned in the paper's tests
    before: BPTT and RTRL already fail at 10 steps
    after: 1,000 steps (Task 2c)
    source: http://www.bioinf.jku.at/publications/older/2604.pdf
  - aspect: Units needed for one memory
    before: One hidden unit
    after: A memory cell plus an input gate and an output gate; at most 3 units replace 1, so weights grow by at most a factor of 9
    source: http://www.bioinf.jku.at/publications/older/2604.pdf
  - aspect: Update cost per weight and time step
    before: 'BPTT: O(1), but past activations must be stored; RTRL: much worse'
    after: O(1), local in space and time
    source: http://www.bioinf.jku.at/publications/older/2604.pdf
key_numbers:
  columns:
  - Task in the 1997 paper
  - Minimal time lag (steps)
  - Weights
  - Training sequences until success
  - Result
  rows:
  - cells:
    - Embedded Reber grammar (Experiment 1), 3 blocks of 2 cells
    - as few as 9
    - '276'
    - 8,440 (mean of 30 trials)
    - 100% of trials solved at learning rate 0.5
    source: http://www.bioinf.jku.at/publications/older/2604.pdf
  - cells:
    - Long lag with distractors (Task 2c), 100 distractor symbols
    - '101'
    - '664'
    - 31,000 (mean of 20 trials)
    - solved; BPTT and RTRL have no chance on this task
    source: http://www.bioinf.jku.at/publications/older/2604.pdf
  - cells:
    - Long lag with distractors (Task 2c), 1,000 distractor symbols
    - 1,001
    - 6,064
    - 49,000 (mean of 20 trials)
    - solved
    source: http://www.bioinf.jku.at/publications/older/2604.pdf
  - cells:
    - Adding problem (Experiment 4), sequences of at least 1,000 steps
    - '500'
    - '93'
    - 853,000 (mean of 10 trials)
    - 1 wrong out of 2,560 test sequences
    source: http://www.bioinf.jku.at/publications/older/2604.pdf
misunderstandings:
- misunderstanding: The original 1997 LSTM had a forget gate.
  truth: 'The 1997 memory cell has **two gates**, input and output, and updates its state as **s(t) = s(t−1) + y_in(t) · g(net(t))**: nothing ever scales the old value down. The **forget gate** was added by **Gers, Schmidhuber and Cummins (2000)**; the three-gate cell drawn in the figure is that later form.'
  source: http://www.bioinf.jku.at/publications/older/2604.pdf
- misunderstanding: LSTMs fix both vanishing and exploding gradients.
  truth: The gated cell targets **vanishing** error. Sutskever et al. (2014) write that LSTMs tend not to suffer from vanishing gradients but **can have exploding gradients**, so they rescaled the gradient whenever its norm exceeded **5**.
  source: https://arxiv.org/abs/1409.3215
- misunderstanding: An LSTM remembers exactly when something happened.
  truth: 'The 1997 authors write that all gradient-based approaches, LSTM included, have a practical inability to **precisely count discrete time steps**: telling whether a signal came **99 or 100** steps ago needs an extra counting mechanism. Telling 3 from 11 steps poses no problem.'
  source: http://www.bioinf.jku.at/publications/older/2604.pdf
- misunderstanding: The GRU is a newer, strictly better LSTM.
  truth: 'Chung et al. (2014) compared LSTM and GRU units of about the **same parameter count** on music and speech data. Both clearly beat a plain tanh unit, but the authors found the comparison between the two **not conclusive**: which is better may depend on the dataset and task.'
  source: https://arxiv.org/abs/1412.3555
diagram_svg: '<svg class="atlas-figure" viewBox="0 0 900 545" role="img" aria-label="Left: a recurrent network unrolled over three steps, the same cell reading x1, x2, x3 and passing its hidden and cell state forward. Right: inside one LSTM cell, the cell state runs across the top and is changed only by the forget gate''s multiplication and the input gate''s addition; the output gate reads it out through tanh as h_t.">

  <defs><marker id="rnn-lstm-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="rnn-lstm-arrow-strong" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="rnn-lstm-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>

  <text class="d-title" x="165" y="110" text-anchor="middle">Unrolled over time</text>

  <path class="d-line-strong" d="M22 250 H50" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <rect class="d-box" x="52" y="228" width="46" height="44" rx="5"/>

  <text class="d-label" x="75" y="254" text-anchor="middle">LSTM</text>

  <circle class="d-node d-role-input" cx="75" cy="340" r="13"/>

  <text class="d-note" x="75" y="343.5" text-anchor="middle">x₁</text>

  <circle class="d-node d-role-output" cx="75" cy="160" r="13"/>

  <text class="d-note" x="75" y="163.5" text-anchor="middle">h₁</text>

  <path class="d-line-strong" d="M75 327 V274" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M75 228 V175" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M98 250 H140" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <text class="d-note" x="120" y="242" text-anchor="middle">h₁,c₁</text>

  <rect class="d-box" x="142" y="228" width="46" height="44" rx="5"/>

  <text class="d-label" x="165" y="254" text-anchor="middle">LSTM</text>

  <circle class="d-node d-role-input" cx="165" cy="340" r="13"/>

  <text class="d-note" x="165" y="343.5" text-anchor="middle">x₂</text>

  <circle class="d-node d-role-output" cx="165" cy="160" r="13"/>

  <text class="d-note" x="165" y="163.5" text-anchor="middle">h₂</text>

  <path class="d-line-strong" d="M165 327 V274" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M165 228 V175" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M188 250 H230" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <text class="d-note" x="210" y="242" text-anchor="middle">h₂,c₂</text>

  <rect class="d-box" x="232" y="228" width="46" height="44" rx="5"/>

  <text class="d-label" x="255" y="254" text-anchor="middle">LSTM</text>

  <circle class="d-node d-role-input" cx="255" cy="340" r="13"/>

  <text class="d-note" x="255" y="343.5" text-anchor="middle">x₃</text>

  <circle class="d-node d-role-output" cx="255" cy="160" r="13"/>

  <text class="d-note" x="255" y="163.5" text-anchor="middle">h₃</text>

  <path class="d-line-strong" d="M255 327 V274" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M255 228 V175" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M278 250 H305" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <text class="d-note" x="165" y="385" text-anchor="middle">one cell, the same weights,</text>

  <text class="d-note" x="165" y="399" text-anchor="middle">applied at every step</text>

  <rect class="d-frame" x="330" y="60" width="500" height="370" rx="8"/>

  <text class="d-title" x="580" y="46" text-anchor="middle">Inside one LSTM cell at step t</text>

  <path class="d-line-accent" d="M330 110 H885" marker-end="url(#rnn-lstm-arrow-accent)"/>

  <text class="d-math" x="340" y="100" text-anchor="start">cₜ₋₁</text>

  <text class="d-math" x="879" y="100" text-anchor="end">cₜ</text>

  <text class="d-note" x="668" y="90" text-anchor="middle">cell state: only scaled (×) and added to (+)</text>

  <circle class="d-node" cx="420" cy="110" r="11"/>

  <text class="d-label" x="420" y="114" text-anchor="middle">×</text>

  <circle class="d-node" cx="555" cy="110" r="11"/>

  <text class="d-label" x="555" y="114" text-anchor="middle">+</text>

  <path class="d-line-strong" d="M330 360 H700"/>

  <text class="d-math" x="340" y="350" text-anchor="start">hₜ₋₁</text>

  <path class="d-line-strong" d="M380 450 V360"/>

  <circle class="d-node" cx="380" cy="360" r="3"/>

  <text class="d-math" x="380" y="466" text-anchor="middle">xₜ</text>

  <text class="d-math" x="560" y="378" text-anchor="middle">[hₜ₋₁, xₜ]</text>

  <path class="d-line-strong" d="M420 360 V309" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <rect class="d-box d-role-router" x="384" y="267" width="72" height="40" rx="5"/>

  <text class="d-title" x="420" y="284" text-anchor="middle">σ</text>

  <text class="d-note" x="420" y="300" text-anchor="middle">forget fₜ</text>

  <path class="d-line-strong" d="M510 360 V309" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <rect class="d-box d-role-router" x="474" y="267" width="72" height="40" rx="5"/>

  <text class="d-title" x="510" y="284" text-anchor="middle">σ</text>

  <text class="d-note" x="510" y="300" text-anchor="middle">input iₜ</text>

  <path class="d-line-strong" d="M600 360 V309" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <rect class="d-box d-role-feedforward" x="564" y="267" width="72" height="40" rx="5"/>

  <text class="d-title" x="600" y="284" text-anchor="middle">tanh</text>

  <text class="d-note" x="600" y="300" text-anchor="middle">candidate gₜ</text>

  <path class="d-line-strong" d="M700 360 V309" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <rect class="d-box d-role-router" x="664" y="267" width="72" height="40" rx="5"/>

  <text class="d-title" x="700" y="284" text-anchor="middle">σ</text>

  <text class="d-note" x="700" y="300" text-anchor="middle">output oₜ</text>

  <path class="d-line-strong" d="M420 267 V123" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M510 267 V200 H542" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M600 267 V200 H568" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <circle class="d-node" cx="555" cy="200" r="11"/>

  <text class="d-label" x="555" y="204" text-anchor="middle">×</text>

  <path class="d-line-strong" d="M555 189 V123" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M760 110 V176" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <rect class="d-box d-role-feedforward" x="735" y="178" width="50" height="24" rx="5"/>

  <text class="d-label" x="760" y="194" text-anchor="middle">tanh</text>

  <path class="d-line-strong" d="M760 202 V274" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <path class="d-line-strong" d="M736 287 H747" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <circle class="d-node" cx="760" cy="287" r="11"/>

  <text class="d-label" x="760" y="291" text-anchor="middle">×</text>

  <path class="d-line-strong" d="M760 298 V400 H885" marker-end="url(#rnn-lstm-arrow-strong)"/>

  <text class="d-math" x="879" y="390" text-anchor="end">hₜ</text>

  <text class="d-math" x="340" y="492" text-anchor="start">fₜ, iₜ, oₜ = σ(W·[hₜ₋₁, xₜ] + b)</text>

  <text class="d-math" x="620" y="492" text-anchor="start">gₜ = tanh(W·[hₜ₋₁, xₜ] + b)</text>

  <text class="d-math" x="340" y="512" text-anchor="start">cₜ = fₜ ⊙ cₜ₋₁ + iₜ ⊙ gₜ</text>

  <text class="d-math" x="620" y="512" text-anchor="start">hₜ = oₜ ⊙ tanh(cₜ)</text>

  <text class="d-note" x="340" y="534" text-anchor="start">each gate has its own W and b · ⊙ = element-wise product, drawn as ×</text>

  <text class="d-note" x="20" y="492" text-anchor="start">LSTM: Hochreiter &amp; Schmidhuber 1997</text>

  <text class="d-note" x="20" y="507" text-anchor="start">forget gate added by Gers,</text>

  <text class="d-note" x="20" y="522" text-anchor="start">Schmidhuber &amp; Cummins 2000</text>

  </svg>

  '
diagram_caption: 'One recurrent cell reused at every time step (left), and inside it the LSTM: the cell state runs straight across, scaled by the forget gate and added to by the input gate, while the output gate reads h_t out through tanh.'
notable_models:
- name: LSTM
  year: 1997
  note: Hochreiter & Schmidhuber's gated cell (input and output gates); the authors report bridging time lags in excess of 1000 steps.
- name: LSTM with forget gate
  year: 2000
  note: Gers, Schmidhuber & Cummins added an adaptive forget gate that lets the cell learn to reset its state.
- name: Seq2seq LSTM translation
  year: 2014
  note: Sutskever, Vinyals & Le used one 4-layer LSTM (1,000 cells per layer) to read the source sentence and another to write the translation; 34.8 BLEU on WMT 2014 English to French, with reversed source sentences.
- name: GRU
  year: 2014
  note: A gated unit proposed by Cho et al. as motivated by the LSTM but simpler to compute; Chung et al. found it comparable to LSTM on music and speech signal modelling.
resources:
- kind: paper
  title: Long Short-Term Memory (Hochreiter & Schmidhuber, 1997)
  url: https://doi.org/10.1162/neco.1997.9.8.1735
- kind: blog
  title: Understanding LSTM Networks (Chris Olah)
  url: https://colah.github.io/posts/2015-08-Understanding-LSTMs/
- kind: blog
  title: The Unreasonable Effectiveness of Recurrent Neural Networks (Andrej Karpathy)
  url: https://karpathy.github.io/2015/05/21/rnn-effectiveness/
- kind: tutorial
  title: Recurrent Neural Networks (Stanford CS231n notes)
  url: https://cs231n.github.io/
learn_first:
- label: MLP & backpropagation
  id: architecture-era/mlp-backprop
  tag: Architecture
- label: Backpropagation through time
  tag: Concept
- label: Vanishing & exploding gradients
  tag: Concept
tags:
- rnn
- lstm
- sequence
- classical-dl
- foundational
updated: '2026-11-23'
sources:
- url: http://www.bioinf.jku.at/publications/older/2604.pdf
  title: Long Short-Term Memory (Hochreiter & Schmidhuber, Neural Computation 9(8), 1735-1780, 1997; doi:10.1162/neco.1997.9.8.1735)
  trust: 0.95
- url: https://doi.org/10.1162/089976600300015015
  title: 'Learning to Forget: Continual Prediction with LSTM (Gers, Schmidhuber & Cummins, 2000, Neural Computation 12(10))'
  trust: 0.9
- url: https://www.jmlr.org/papers/volume3/gers02a/gers02a.pdf
  title: Learning Precise Timing with LSTM Recurrent Networks (Gers, Schraudolph & Schmidhuber, JMLR 3, 2002)
  trust: 0.95
- url: https://arxiv.org/abs/1409.0473
  title: Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho & Bengio, arXiv:1409.0473)
  trust: 0.95
- url: https://arxiv.org/abs/1406.1078
  title: Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation (Cho et al., arXiv:1406.1078)
  trust: 0.95
- url: https://arxiv.org/abs/1412.3555
  title: Empirical Evaluation of Gated Recurrent Neural Networks on Sequence Modeling (Chung et al., arXiv:1412.3555)
  trust: 0.95
- url: https://arxiv.org/abs/1409.3215
  title: Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals & Le, arXiv:1409.3215)
  trust: 0.95
- url: https://arxiv.org/abs/2312.00752
  title: 'Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, arXiv:2312.00752)'
  trust: 0.95
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., arXiv:1706.03762)
  trust: 0.95
---

## What Changed

A **plain recurrent network** recomputes every unit from the previous step's values, so a stored
value is squashed again at every step. The **LSTM** keeps its memory in a cell that is only added to.

How to read the table above (it restates the 1997 paper's Sections 3 and 4):

- **Minimal time lag:** the shortest gap, in steps, between an input and the target that depends on
  it. A long lag means the network must hold the input in memory the whole time.
- **Error sent back:** training adjusts the weights from an error signal that travels backwards
  through the steps. Each step multiplies it by a factor; many factors below 1 make it vanish.
- **O(1)**, read "order of one": the cost per weight and step does not grow with the network or the
  sequence.
- **The price:** extra gate units and weights for every memory cell.

## Key Ideas

- **Recurrent state:** one network is applied at every step and passes its state forward, so each
  output can depend on everything read so far.
- **Vanishing error:** the error sent back through a plain recurrent network is multiplied by a
  factor at every step and shrinks, or grows, exponentially with the number of steps.
- **Constant error carousel:** a cell that keeps its value from step to step passes the error back
  unscaled.
- **Gates:** sigmoid units between 0 and 1 multiply what is written into, read from and (since 2000)
  kept in the cell.

## How It Works

The figure above has **two halves**. On the left the same cell is drawn once per step of the
sequence; on the right is what happens inside that cell at one step t.

A **vector** is a list of numbers. A **sigmoid** (σ) turns any number into one between 0 and 1;
**tanh** turns any number into one between −1 and 1.

1. **Unroll over time (left).** The sequence arrives one element at a time: x₁, x₂, x₃. The **same
   cell, with the same weights**, reads each one and passes two things to the next step: the output
   h and the cell state c.
2. **Gather the inputs of this step.** Inside the cell, the previous output hₜ₋₁ and the new input
   xₜ are joined into one vector, [hₜ₋₁, xₜ], which feeds all four boxes in the middle row.
3. **Compute three gates.** Each σ box multiplies [hₜ₋₁, xₜ] by its own learned weights, adds a bias
   and applies a sigmoid: the **forget gate** fₜ, the **input gate** iₜ and the **output gate** oₜ.
4. **Compute a candidate.** The tanh box does the same with its own weights and a tanh, giving the
   candidate value gₜ that could be written into memory.
5. **Update the cell state (top line).** The old state cₜ₋₁ is multiplied by fₜ (the × circle), and
   iₜ × gₜ is added (the + circle): cₜ = fₜ ⊙ cₜ₋₁ + iₜ ⊙ gₜ. This line is never squashed, which is
   why a value can travel far along it.
6. **Read out.** The new state passes through tanh and is multiplied by the output gate:
   hₜ = oₜ ⊙ tanh(cₜ). Both cₜ and hₜ go to the next step; hₜ is also the cell's output.
7. **The 1997 cell.** Remove the forget gate (fₜ = 1 always): the state is
   s(t) = s(t−1) + y_in(t) · g(net(t)) and the output is y_out(t) · h(s(t)). In most of the
   paper's experiments g and h were sigmoids scaled to the ranges [−2, 2] and [−1, 1], not tanh.
8. **Many cells, many layers.** A real layer has many such cells side by side. Sutskever et al.
   (2014) stacked **4 layers of 1,000 cells** to read a sentence and another 4 to write its
   translation.

## Worked Example

**Why a plain recurrent network forgets and an LSTM cell does not**, with one number per state
instead of a vector. All weights and gate values are made up so every step can be checked by hand:
an example, not from a trained model.

**Part 1: a plain recurrent network.** The rule is hₜ = tanh(0.5 × hₜ₋₁ + xₜ). An important input
x₁ = 2 arrives, then two empty steps (x₂ = x₃ = 0).

1. **Step 1:** h₁ = tanh(0.5 × 0 + 2) = tanh(2) ≈ **0.964**.
2. **Step 2:** h₂ = tanh(0.5 × 0.964) = tanh(0.482) ≈ **0.448**.
3. **Step 3:** h₃ = tanh(0.5 × 0.448) = tanh(0.224) ≈ **0.220**. The memory of x₁ has faded to less
   than a quarter in two steps.
4. **The error sent back.** How much h₃ changes when h₁ changes is a product of one factor per step,
   0.5 × (1 − hₜ²), because the slope of tanh at hₜ is 1 − hₜ²:
   - step 3: 0.5 × (1 − 0.220²) = 0.5 × 0.952 ≈ 0.476
   - step 2: 0.5 × (1 − 0.448²) = 0.5 × 0.799 ≈ 0.400
   - product: 0.476 × 0.400 ≈ **0.190**
5. **Over more steps.** Every factor here is at most 0.5, so after 10 steps the error is at most
   0.5¹⁰ ≈ **0.001** of its size. This is the vanishing the 1997 paper analyses.

**Part 2: one LSTM cell.** Each gate's weighted sum is given directly; σ is the sigmoid. The cell
starts at c₀ = 0.

1. **Step 1, write.** Input gate σ(3) ≈ 0.9526, candidate tanh(2) ≈ 0.9640, forget gate σ(0) = 0.5
   (it multiplies c₀ = 0, so it has no effect), output gate σ(0) = 0.5.
   - c₁ = 0.5 × 0 + 0.9526 × 0.9640 ≈ **0.9183**
   - h₁ = 0.5 × tanh(0.9183) = 0.5 × 0.7251 ≈ **0.363**
2. **Step 2, a distraction.** Forget gate σ(4) ≈ 0.9820, input gate σ(−3) ≈ 0.0474, candidate
   tanh(−1) ≈ −0.7616, output gate σ(−2) ≈ 0.1192.
   - kept: 0.9820 × 0.9183 ≈ 0.9018; added: 0.0474 × (−0.7616) ≈ −0.0361
   - c₂ = 0.9018 − 0.0361 = **0.8657**; h₂ = 0.1192 × tanh(0.8657) = 0.1192 × 0.6992 ≈ **0.083**
3. **Step 3, read.** Forget gate σ(4) ≈ 0.9820, input gate σ(−3) ≈ 0.0474, candidate tanh(0) = 0,
   output gate σ(3) ≈ 0.9526.
   - c₃ = 0.9820 × 0.8657 + 0.0474 × 0 ≈ **0.8501**
   - h₃ = 0.9526 × tanh(0.8501) = 0.9526 × 0.6911 ≈ **0.658**
4. **The error sent back along the cell state.** On the top line of the figure, c₃ depends on c₁
   only through the two forget gates: 0.9820 × 0.9820 ≈ **0.964**, against 0.190 in Part 1. After 10
   such steps it would still be 0.982¹⁰ ≈ 0.834. In the 1997 cell, with no forget gate, the factor is
   exactly 1.

**What this means:** the plain network's memory of x₁ fell from 0.964 to 0.220 in two steps, and
its error signal shrank to 0.190. The LSTM cell held 0.9183 at 0.8501, kept the distraction out with
a nearly closed input gate, and released the memory only at step 3, when the output gate opened.
