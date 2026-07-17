---
id: architecture-era/seq2seq
type: architecture-era
slug: seq2seq
schema_version: 1.0
title: "Sequence-to-Sequence Learning"
name: "Sequence-to-Sequence (Encoder–Decoder)"
era: foundation
era_range: "2014–2016"
year: 2014
family: classical-dl
core_innovation: "An encoder RNN compresses a whole input sequence into a fixed vector that a decoder RNN unrolls into an output sequence."
description: "The encoder–decoder framing that turned neural nets into general sequence transducers for translation."
key_features: ["Encoder–decoder RNN pair", "Fixed-length context vector", "Variable-length input to variable-length output", "End-to-end trained on raw pairs"]
key_models: ["Google/Sutskever NMT (2014)", "Neural machine translation systems (2015–2016)"]
paper_title: "Sequence to Sequence Learning with Neural Networks"
paper_url: "https://arxiv.org/abs/1409.3215"
influenced_by: [architecture-era/rnn-lstm]
led_to: [architecture-era/attention-additive]
tags: [seq2seq, encoder-decoder, translation, classical-dl, foundational]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1409.3215", title: "Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals & Le, 2014)", trust: 0.95 }
---

## What Changed
Earlier neural sequence models mapped an input to a *same-length* output — good for tagging, useless
for translation, where a five-word English sentence becomes a seven-word French one. Sutskever, Vinyals,
and Le framed the problem as **sequence-to-sequence**: one LSTM (the encoder) reads the entire source
sequence and folds it into a single fixed-length vector, and a second LSTM (the decoder) generates the
target sequence one token at a time, conditioned on that vector and its own previous outputs. This gave
a fully learned, end-to-end machine-translation system trained on raw sentence pairs — no hand-built
alignment or phrase tables. Its brilliance was also its flaw: forcing an arbitrarily long input through
one fixed vector creates an information bottleneck that gets worse as sentences grow. That bottleneck is
the exact problem the next step — additive attention — was invented to remove.

## Key Ideas
- **Encoder–decoder split** — one network summarises the input, a separate network generates the output,
  cleanly handling different input and output lengths.
- **Fixed context vector** — the encoder's final hidden state must carry the whole meaning of the
  source, an unavoidable bottleneck for long sequences.
- **Autoregressive decoding** — the decoder emits one token at a time, feeding each output back as the
  next input (with teacher forcing during training).
- **Practical tricks** — reversing the source sequence and stacking LSTM layers materially improved
  translation quality, hinting that the fixed vector was the weak link.
