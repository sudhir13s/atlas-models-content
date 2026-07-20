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

eli5: "A recurrent neural network reads a sentence one word at a time and keeps a little 'memory' that it updates at every word — so what it thinks about word ten depends on words one through nine. Plain versions forget too fast. The LSTM adds gates that decide what to remember and what to throw away, so it can hold onto important details across a long stretch."

analogy:
  title: "Reading a novel while keeping running notes"
  body: "Imagine reading a mystery novel and keeping a notebook of what matters — the culprit's alibi, a hidden key, a name dropped in chapter two. At each new page you do three things: decide what old notes to *erase* because they no longer matter (the forget gate), decide what new details to *write down* (the input gate), and decide what to *glance at* right now to make sense of the page (the output gate). Your notebook is the LSTM's memory cell; because you edit it deliberately rather than rewriting it from scratch each page, a clue from chapter two survives to the finale. A plain RNN is like trying to hold everything in your head with no notebook — by chapter twenty the early clues have blurred away."

narrative:
  motivation: "Feed-forward networks treat every input independently — they have no memory and no sense of order, which is fatal for language, speech, and time series where meaning lives in the sequence. A recurrent network fixes the framing by threading a hidden state through time, so each output depends on everything seen so far. But there was a crippling training problem."
  approach: "Training an RNN means backpropagating through time — unrolling the recurrence into one long chain and applying the chain rule across every step. That chain multiplies many gradient terms together, and long products of numbers either shrink toward zero (vanishing gradients) or blow up (exploding gradients). Hochreiter diagnosed this precisely: plain RNNs simply cannot learn dependencies more than a handful of steps apart."
  solution: "The LSTM introduces a dedicated memory cell whose state is updated by *addition* rather than repeated multiplication — the 'constant error carousel' — so gradients can travel across hundreds of steps without vanishing. Three learned gates control the flow: a forget gate erases stale memory, an input gate writes new information, and an output gate decides what of the memory to expose at each step. The network learns when to remember and when to forget."
  outcome: "This made long-range sequence learning practical, and for roughly two decades LSTMs (and the streamlined GRU) were the default for translation, speech recognition, and handwriting. They powered the first neural machine-translation systems via the encoder–decoder (seq2seq) design. Their core idea — a selectively, additively updated recurrent state — even resurfaces in today's linear-time state-space models like Mamba. Their one unfixable flaw, strict step-by-step computation, is what the transformer later removed."

build_steps:
  - { title: "1. Carry a hidden state", detail: "Give the network a hidden state vector h that is passed from one time step to the next. It is the network's running memory of everything read so far." }
  - { title: "2. Update it at every step", detail: "At step t, combine the new input x_t with the previous state h_(t−1) through learned weights, apply a non-linearity, and produce the new state h_t. This recurrence is the whole of a plain RNN." }
  - { title: "3. Spot the gradient problem", detail: "Training unrolls the recurrence and backpropagates through time. Multiplying gradients across many steps makes them vanish or explode, so a plain RNN can't learn long-range links." }
  - { title: "4. Add a protected memory cell", detail: "The LSTM adds a separate cell state c that is edited mainly by addition, not multiplication. This additive path lets gradients flow far back in time undamaged — the constant error carousel." }
  - { title: "5. Gate the flow", detail: "Three sigmoid gates (values between 0 and 1) control the cell: the forget gate scales down old memory, the input gate lets new content in, and the output gate decides what to read out as h_t." }
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
  - "The LSTM's additive memory cell carries information and gradients across hundreds of steps."
  - "Gates let the network learn what to remember and forget rather than hard-coding it."
  - "Processes streams online, updating its state one step at a time as data arrives."

weaknesses:
  - "Strictly sequential — step t must wait for step t−1, so training can't parallelize across time."
  - "Even LSTMs struggle with very long-range dependencies and lose the earliest context."
  - "The gated cell is more complex and slower per step than a simple feed-forward layer."
  - "A fixed-size state is a bottleneck: everything seen must be squeezed into one vector."

diagram: |
  flowchart LR
    X1["Input x1"] --> C1["LSTM cell t=1"]
    C1 -->|"state h1, c1"| C2["LSTM cell t=2"]
    X2["Input x2"] --> C2
    C2 -->|"state h2, c2"| C3["LSTM cell t=3"]
    X3["Input x3"] --> C3
    C1 --> Y1["Output y1"]
    C2 --> Y2["Output y2"]
    C3 --> Y3["Output y3"]
diagram_caption: "The same LSTM cell is applied at each step, passing its hidden and memory state forward so later steps see earlier context."

notable_models:
  - { name: "LSTM", year: 1997, note: "Hochreiter & Schmidhuber's gated cell — proved recurrent nets can learn dependencies hundreds of steps long." }
  - { name: "Seq2seq LSTM translation", year: 2014, note: "Stacked LSTMs as encoder and decoder — proved end-to-end neural machine translation was viable." }
  - { name: "GRU", year: 2014, note: "Cho et al.'s streamlined two-gate cell — proved you can get LSTM-like memory with fewer parameters." }

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
updated: 2026-07-20
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
