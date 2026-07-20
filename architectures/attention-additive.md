---
id: architecture-era/attention-additive
type: architecture-era
slug: attention-additive
schema_version: 1.0
title: "Additive Attention"
name: "Additive (Bahdanau) Attention"
era: foundation
era_range: "2015–2017"
year: 2015
family: classical-dl
core_innovation: "The decoder learns a soft, content-based weighting over all encoder states each step, replacing the single fixed context vector."
description: "Bahdanau attention — the mechanism that dissolved the seq2seq bottleneck and seeded the transformer."
key_features: ["Soft alignment over encoder states", "Learned additive scoring network", "Per-step dynamic context vector", "Differentiable, jointly trained"]
key_models: ["Bahdanau NMT (2015)", "Luong attention NMT (2015)"]
paper_title: "Neural Machine Translation by Jointly Learning to Align and Translate"
paper_url: "https://arxiv.org/abs/1409.0473"
influenced_by: [architecture-era/seq2seq]
led_to: [architecture-era/transformer]

eli5: "Old translators squeezed a whole sentence into one tiny summary and then translated from that — long sentences got mangled. Additive attention lets the translator glance back at every original word each time it writes a new one, focusing on the few words that matter right now."

analogy:
  title: "A soft spotlight over the source sentence"
  body: "Picture the source sentence spread out on a table, one word per card. As the translator writes each new output word, it sweeps a spotlight across all the cards — but instead of landing on one card, the light spreads: bright on the two or three words that matter now, dim everywhere else. It then reads a blend of the lit-up cards. Every output word gets its own spotlight setting, and the brightness levels are learned, not fixed. That adjustable, soft spotlight is additive attention."

narrative:
  motivation: "The 2014 sequence-to-sequence (seq2seq) recipe encoded an entire source sentence into a single fixed-length vector, then decoded the translation from it. That one vector is a bottleneck: a 40-word sentence and a 4-word sentence get the same amount of room, so quality collapsed on long inputs — the model simply could not hold everything in one summary."
  approach: "Bahdanau, Cho, and Bengio asked a simple question: why force the decoder to work from one summary when the encoder already produced a vector for every source position? Instead of discarding those per-word states, keep all of them and let the decoder decide, at each step, which ones to read."
  solution: "At every decoding step a small feed-forward network scores how well the decoder's current state aligns with each encoder state. A softmax turns those scores into weights that sum to one, and the weighted average of the encoder states becomes a context vector — recomputed fresh for every output token. Because the scoring network is differentiable, the alignment is learned jointly with translation, not hand-designed."
  outcome: "Translation quality stopped degrading on long sentences, and the learned weights, when plotted, formed a soft word-alignment map for free — you could see the model line up 'European Economic Area' with 'zone économique européenne'. Conceptually it was the leap: a representation is not one static summary but a dynamic, content-addressed read over memory. Generalize that idea — drop recurrence and let every token attend to every other — and you get the transformer two years later."

build_steps:
  - { title: "1. Encode every source word", detail: "Run the source sentence through an encoder (originally a bidirectional RNN) to get one hidden state per word — a bank of vectors, not a single summary." }
  - { title: "2. Score alignment for each word", detail: "At decoding step t, take the decoder's current state and compare it against each encoder state with a small feed-forward net, producing one raw relevance score (energy) per source word." }
  - { title: "3. Softmax into attention weights", detail: "Push the scores through a softmax so they become positive weights that sum to one — a soft distribution over which source words to read now." }
  - { title: "4. Build the context vector", detail: "Take the weighted average of the encoder states using those weights. This is the context vector for step t: mostly the words the model deems relevant, a little of the rest." }
  - { title: "5. Emit the next word", detail: "Feed the context vector plus the decoder state into the output layer to predict the next translated token, then advance to step t+1 and recompute a brand-new context." }

key_equation:
  latex: "e_{tj}=v^{\\top}\\tanh\\!\\left(W\\,[\\,s_{t-1};\\,h_j\\,]\\right)"
  plain: "To score how relevant source word j is at output step t, glue the decoder's previous state and that word's encoder state together, pass them through one small tanh layer, and project down to a single number. Do this for every source word, then softmax those numbers into attention weights."
  terms:
    - { symbol: "e_{tj}", meaning: "alignment score (energy) between output step t and source word j — higher means more relevant" }
    - { symbol: "s_{t-1}", meaning: "the decoder's hidden state just before producing output word t (what am I looking for?)" }
    - { symbol: "h_j", meaning: "the encoder's hidden state for source word j (what does this word offer?)" }
    - { symbol: "W", meaning: "learned weight matrix that mixes the concatenated decoder and encoder states" }
    - { symbol: "v", meaning: "learned vector that collapses the tanh layer's output to one scalar score" }

strengths:
  - "Removes the fixed-vector bottleneck — long sentences no longer lose information, since every source word stays directly reachable."
  - "The attention weights are interpretable: plotting them yields a soft word-alignment map as a free by-product."
  - "Fully differentiable, so alignment is learned end-to-end with translation rather than engineered."
  - "Context is dynamic — each output word gets its own focus over the source, matching how translation actually reorders words."

weaknesses:
  - "Still built on recurrence, so training remains sequential and cannot fully parallelize across time."
  - "The additive scoring net is computed for every (output, source) pair, adding cost that grows with sentence length."
  - "Attention here connects decoder to encoder only — the encoder still can't attend within itself, a limit the transformer later removes."

diagram: |
  flowchart LR
    subgraph SRC["Encoder states"]
      H1["h1"]
      H2["h2"]
      H3["h3"]
    end
    S["Decoder state s(t-1)"] --> SCORE["Additive score + softmax"]
    H1 --> SCORE
    H2 --> SCORE
    H3 --> SCORE
    SCORE --> W["Attention weights (sum to 1)"]
    W --> CTX["Context vector = weighted avg"]
    CTX --> OUT["Predict next word"]
diagram_caption: "Each decoding step scores the decoder state against every encoder state, softmaxes into weights, and reads a weighted blend — a fresh context vector per output word."

notable_models:
  - { name: "Bahdanau NMT", year: 2015, note: "The original — proved a learned soft alignment fixes the seq2seq long-sentence bottleneck and beats fixed-vector encoders." }
  - { name: "Luong attention", year: 2015, note: "Simplified scoring to a dot-product/bilinear form (global vs local attention), showing the mechanism generalizes beyond the additive net." }
  - { name: "Pointer Networks", year: 2015, note: "Reused attention weights themselves as the output — pointing at input positions to solve ordering and copy tasks." }
  - { name: "Transformer", year: 2017, note: "Generalized attention into the whole model, dropping recurrence and applying it within the encoder too." }

resources:
  - { kind: paper, title: "Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau et al., 2015)", url: "https://arxiv.org/abs/1409.0473" }
  - { kind: blog, title: "Attention and Augmented Recurrent Neural Networks (Distill)", url: "https://distill.pub/2016/augmented-rnns/" }
  - { kind: blog, title: "Attention? Attention! (Lilian Weng)", url: "https://lilianweng.github.io/posts/2018-06-24-attention/" }
  - { kind: blog, title: "Visualizing A Neural Machine Translation Model (Jay Alammar)", url: "https://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/" }

learn_first:
  - { label: "Seq2seq & the bottleneck it hit", href: "/models/architecture/seq2seq", tag: "Architecture" }
  - { label: "Softmax turns scores into weights", href: "/learn/concepts/softmax", tag: "Math" }
  - { label: "Recurrent networks (RNN/LSTM)", href: "/learn/concepts/rnn", tag: "Architecture" }

tags: [attention, alignment, translation, classical-dl, foundational]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/1409.0473", title: "Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho & Bengio, 2015)", trust: 0.95 }
  - { url: "https://lilianweng.github.io/posts/2018-06-24-attention/", title: "Attention? Attention! (Lilian Weng)", trust: 0.9 }
  - { url: "https://distill.pub/2016/augmented-rnns/", title: "Attention and Augmented Recurrent Neural Networks (Distill)", trust: 0.9 }
---

## What Changed
Seq2seq crammed an entire source sentence into one fixed vector; long sentences suffered because that
vector could not hold everything. Bahdanau, Cho, and Bengio removed the bottleneck by letting the
decoder *look back* at all of the encoder's per-position states at every generation step.

A small scoring network compares the decoder's current state to each encoder state, a softmax turns
those scores into weights that sum to one, and the weighted average of encoder states becomes a
**context vector recomputed fresh for each output token**. Because the weights are differentiable, the
whole alignment is learned jointly with translation — and inspecting them reveals a soft word-alignment
matrix as a free by-product.

This was the conceptual leap: representation is not a single summary but a *dynamic, content-addressed
read* over memory. Generalising it — dropping recurrence and applying attention everywhere, including
within the encoder — is exactly what "Attention Is All You Need" did two years later.

## Key Ideas
- **Soft alignment** — instead of one summary vector, the decoder attends over every encoder state,
  so information from any source position is directly reachable.
- **Additive scoring** — relevance is scored by a small feed-forward net, $v^\top \tanh(W_1 h_j + W_2 s_{t-1})$,
  then normalised by softmax into attention weights.
- **Dynamic context** — the context vector is re-derived at each decoding step, so the model focuses on
  different source words as it generates.
- **Interpretability** — the learned attention weights form a soft alignment map, showing which source
  tokens drive each output token.
