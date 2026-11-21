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
key_features: ["Recurrent hidden state", "LSTM memory cell with a constant error carousel", "Multiplicative gates controlling the memory cell", "Backpropagation through time"]
key_models: ["LSTM (1997)", "LSTM with forget gate (2000)", "Gated recurrent unit, GRU (2014)", "Sequence-to-sequence LSTM translation (2014)"]
paper_title: "Long Short-Term Memory"
paper_url: "https://doi.org/10.1162/neco.1997.9.8.1735"
influenced_by: [architecture-era/mlp-backprop]
led_to: [architecture-era/seq2seq, architecture-era/mamba-ssm]

eli5: "A recurrent neural network reads a sentence one word at a time and keeps a little 'memory' that it updates at every word — so what it thinks about word ten depends on words one through nine. Plain versions forget too fast. The LSTM adds gates that decide what to remember and what to throw away, so it can hold onto important details across a long stretch."

analogy:
  title: "Reading a novel while keeping running notes"
  body: "Imagine reading a mystery novel and keeping a notebook of what matters — the culprit's alibi, a hidden key, a name dropped in chapter two. At each new page you do three things: decide what old notes to *erase* because they no longer matter (the forget gate), decide what new details to *write down* (the input gate), and decide what to *glance at* right now to make sense of the page (the output gate). Your notebook is the LSTM's memory cell; because you edit it deliberately rather than rewriting it from scratch each page, a clue from chapter two survives to the finale. A plain RNN is like trying to hold everything in your head with no notebook — by chapter twenty the early clues have blurred away."

narrative:
  motivation: "Feed-forward networks treat every input independently — they have no memory and no sense of order, which is fatal for language, speech, and time series where meaning lives in the sequence. A recurrent network fixes the framing by threading a hidden state through time, so each output depends on everything seen so far. But there was a crippling training problem."
  approach: "Training an RNN means backpropagating through time — unrolling the recurrence into one long chain and applying the chain rule across every step. The LSTM paper states that error signals flowing backwards in time tend to either blow up or vanish, because the backpropagated error depends exponentially on the size of the weights (Hochreiter, 1991); learning to bridge long time lags then takes a prohibitive amount of time or fails."
  solution: "The LSTM introduces a dedicated memory cell with constant error flow through it, the 'constant error carousel'. The authors report that LSTM can bridge time lags in excess of 1000 discrete time steps. In the 1997 cell an input gate protects the memory from irrelevant inputs and an output gate decides what of the memory to expose at each step. Gers, Schmidhuber and Cummins (2000) added a forget gate that lets the cell learn to reset its state."
  outcome: "Stacked LSTMs served as encoder and decoder in Sutskever, Vinyals and Le's 2014 sequence-to-sequence translation model. Mamba (2023) also lets its state selectively propagate or forget information along the sequence. Recurrence computes step by step; the Transformer (2017) dispenses with recurrence and is more parallelizable."

build_steps:
  - { title: "1. Carry a hidden state", detail: "Give the network a hidden state vector h that is passed from one time step to the next. It is the network's running memory of everything read so far." }
  - { title: "2. Update it at every step", detail: "At step t, combine the new input x_t with the previous state h_(t−1) through learned weights, apply a non-linearity, and produce the new state h_t. This recurrence is the whole of a plain RNN." }
  - { title: "3. Spot the gradient problem", detail: "Training unrolls the recurrence and backpropagates through time. Multiplying gradients across many steps makes them vanish or explode, so a plain RNN can't learn long-range links." }
  - { title: "4. Add a protected memory cell", detail: "The LSTM adds a separate cell state c with constant error flow through it, the constant error carousel, so error signals can reach far back in time." }
  - { title: "5. Gate the flow", detail: "Multiplicative gates control the cell. The 1997 cell has an input gate that lets new content in and an output gate that decides what to read out as h_t; the forget gate, added in 2000, scales down old memory." }
  - { title: "6. Unroll over the sequence", detail: "Apply the gated cell step by step across the whole sequence, carrying c and h forward, so information written early can survive to influence a decision made much later." }

key_equation:
  latex: "h_t = f\\!\\left(W x_t + U h_{t-1} + b\\right)"
  plain: "The new hidden state at step t is computed from two things: the current input and the previous hidden state, each passed through learned weights, summed with a bias, and squashed by a non-linearity. That dependence of h_t on h_(t−1) is the recurrence — the loop that gives the network memory."
  terms:
    - { symbol: "h_t", meaning: "the hidden state at step t — the network's memory after reading the t-th input" }
    - { symbol: "h_(t−1)", meaning: "the previous hidden state — memory carried forward from the step before" }
    - { symbol: "x_t", meaning: "the input at step t — the current token, sample, or feature vector" }
    - { symbol: "W, U", meaning: "learned weight matrices for the input and the previous state" }
    - { symbol: "f", meaning: "a non-linear activation (tanh in a plain RNN) that squashes the sum" }

strengths:
  - "Naturally handles variable-length sequences with a fixed-size set of weights."
  - "The authors report that LSTM bridges time lags in excess of 1000 discrete time steps."
  - "Gates let the network learn what to remember and forget rather than hard-coding it."
  - "Processes streams online, updating its state one step at a time as data arrives."

weaknesses:
  - "Strictly sequential — step t must wait for step t−1, so training can't parallelize across time."
  - "The gated cell needs extra gate units per memory cell, so it is more complex than a plain recurrent unit."
  - "A fixed-size state is a bottleneck: everything seen must be squeezed into one vector."

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 900 545" role="img" aria-label="Left: a recurrent network unrolled over three steps, the same cell reading x1, x2, x3 and passing its hidden and cell state forward. Right: inside one LSTM cell, the cell state runs across the top and is changed only by the forget gate's multiplication and the input gate's addition; the output gate reads it out through tanh as h_t.">
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
diagram_caption: "One recurrent cell reused at every time step (left), and inside it the LSTM: the cell state runs straight across, scaled by the forget gate and added to by the input gate, while the output gate reads h_t out through tanh."
notable_models:
  - { name: "LSTM", year: 1997, note: "Hochreiter & Schmidhuber's gated cell (input and output gates); the authors report bridging time lags in excess of 1000 steps." }
  - { name: "LSTM with forget gate", year: 2000, note: "Gers, Schmidhuber & Cummins added an adaptive forget gate that lets the cell learn to reset its state." }
  - { name: "Seq2seq LSTM translation", year: 2014, note: "Sutskever, Vinyals & Le used a multilayered LSTM encoder and a deep LSTM decoder for English-to-French translation." }
  - { name: "GRU", year: 2014, note: "The gated recurrent unit of Cho et al.; Chung et al. found it comparable to LSTM on music and speech signal modeling." }

resources:
  - { kind: paper, title: "Long Short-Term Memory (Hochreiter & Schmidhuber, 1997)", url: "https://doi.org/10.1162/neco.1997.9.8.1735" }
  - { kind: blog, title: "Understanding LSTM Networks (Chris Olah)", url: "https://colah.github.io/posts/2015-08-Understanding-LSTMs/" }
  - { kind: blog, title: "The Unreasonable Effectiveness of Recurrent Neural Networks (Andrej Karpathy)", url: "https://karpathy.github.io/2015/05/21/rnn-effectiveness/" }
  - { kind: tutorial, title: "Recurrent Neural Networks (Stanford CS231n notes)", url: "https://cs231n.github.io/" }

learn_first:
  - { label: "MLP & backpropagation", href: "/models/architecture/mlp-backprop", tag: "Architecture" }
  - { label: "Backpropagation through time", href: "/learn/concepts/backprop-through-time", tag: "Concept" }
  - { label: "Vanishing & exploding gradients", href: "/learn/concepts/vanishing-gradients", tag: "Concept" }

tags: [rnn, lstm, sequence, classical-dl, foundational]
updated: '2026-11-21'
sources:
  - { url: "https://doi.org/10.1162/neco.1997.9.8.1735", title: "Long Short-Term Memory (Hochreiter & Schmidhuber, 1997, Neural Computation 9(8))", trust: 0.9 }
  - { url: "https://doi.org/10.1162/089976600300015015", title: "Learning to Forget: Continual Prediction with LSTM (Gers, Schmidhuber & Cummins, 2000, Neural Computation 12(10))", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1406.1078", title: "Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation (Cho et al., arXiv:1406.1078)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1412.3555", title: "Empirical Evaluation of Gated Recurrent Neural Networks on Sequence Modeling (Chung et al., arXiv:1412.3555)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.3215", title: "Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals & Le, arXiv:1409.3215)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2312.00752", title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, arXiv:2312.00752)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., arXiv:1706.03762)", trust: 0.95 }
---

## What Changed
Feed-forward networks process each input independently. A recurrent network adds a hidden state that
carries forward through time, so the output at step $t$ depends on everything seen so far.

With backpropagation through time, error signals flowing backwards tend to blow up or vanish, so plain
recurrent networks fail to bridge long time lags (Hochreiter, 1991, as reviewed in the LSTM paper).

The **LSTM** (Hochreiter and Schmidhuber, 1997) adds a memory cell with constant error flow, the
"constant error carousel", guarded by multiplicative input and output gates. Gers, Schmidhuber and
Cummins (2000) added the forget gate.

The authors report bridging time lags in excess of 1000 steps. Stacked LSTMs became the encoder and
decoder of 2014 sequence-to-sequence translation.

## Key Ideas
- **Recurrent state** — a hidden vector threads through the sequence, giving the network memory and
  making outputs depend on history.
- **Vanishing/exploding gradients** — backpropagated error depends exponentially on the size of the
  weights, so the signal decays or blows up over long time lags.
- **Gated memory cell** — the LSTM's constant error carousel plus multiplicative gates protect error
  flow across time.
- **Sequential compute** — each step depends on the previous, so training cannot parallelise across
  the time axis; the Transformer dispenses with recurrence.
