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
tags: [attention, alignment, translation, classical-dl, foundational]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1409.0473", title: "Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho & Bengio, 2015)", trust: 0.95 }
---

## What Changed
Seq2seq crammed an entire source sentence into one fixed vector; long sentences suffered because that
vector could not hold everything. Bahdanau, Cho, and Bengio removed the bottleneck by letting the
decoder *look back* at all of the encoder's per-position states at every generation step. A small
scoring network compares the decoder's current state to each encoder state, a softmax turns those
scores into weights that sum to one, and the weighted average of encoder states becomes a **context
vector recomputed fresh for each output token**. Because the weights are differentiable, the whole
alignment is learned jointly with translation — and inspecting them reveals a soft word-alignment
matrix as a free by-product. This was the conceptual leap: representation is not a single summary but
a *dynamic, content-addressed read* over memory. Generalising it — dropping recurrence and applying
attention everywhere, including within the encoder — is exactly what "Attention Is All You Need" did
two years later.

## Key Ideas
- **Soft alignment** — instead of one summary vector, the decoder attends over every encoder state,
  so information from any source position is directly reachable.
- **Additive scoring** — relevance is scored by a small feed-forward net $v^\top \tanh(W_1 h_i + W_2 s_t)$,
  then normalised by softmax into attention weights.
- **Dynamic context** — the context vector is re-derived at each decoding step, so the model focuses on
  different source words as it generates.
- **Interpretability** — the learned attention weights form a soft alignment map, showing which source
  tokens drive each output token.
