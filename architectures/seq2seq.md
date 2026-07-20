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

eli5: "To translate a sentence, one network reads the whole English sentence and squeezes its meaning into a single summary. A second network takes that summary and writes out the French sentence one word at a time. The first network is the 'reader', the second is the 'writer', and the summary is the handoff between them."

analogy:
  title: "An interpreter who listens fully, then speaks"
  body: "Think of a human interpreter at a conference. They don't translate word-for-word as you speak; they *listen to your entire sentence*, form a compact mental gist of what you meant, and only then turn to the other side and render it in the target language, phrase by phrase. Sequence-to-sequence works the same way: the encoder is the listening half that folds the whole source sentence into one mental gist (the context vector), and the decoder is the speaking half that unrolls that gist into the target sentence, one word at a time, each word informed by the words it just said. And like a real interpreter straining to hold a very long sentence in their head, the model struggles when the sentence is long — the single gist can only hold so much."

narrative:
  motivation: "Neural nets were great at mapping an input to a same-length output — tag each word, label each frame. But translation isn't length-preserving: a five-word English sentence might become a seven-word French one, and the word order shuffles. There was no clean neural recipe for turning one variable-length sequence into a different variable-length sequence."
  approach: "Sutskever, Vinyals, and Le split the job in two. One LSTM, the encoder, reads the entire source sequence token by token and ends with a hidden state that summarizes the whole input. A second LSTM, the decoder, starts from that summary and generates the target sequence one token at a time, each new token conditioned on the summary and on the tokens it has already produced."
  solution: "The encoder's final hidden state becomes a single fixed-length 'context vector' — the entire meaning of the source, compressed. The decoder is autoregressive: it emits a word, feeds that word back as its next input, and repeats until it produces an end-of-sentence marker. The whole thing trains end-to-end on raw sentence pairs by maximizing the probability the decoder assigns to the correct target, using teacher forcing to keep training stable."
  outcome: "For the first time a single, fully learned network did competitive machine translation with no hand-built alignment models or phrase tables. Two tricks helped a lot — reversing the source sentence and stacking LSTM layers — and their necessity hinted at the weakness: cramming an arbitrarily long sentence through one fixed vector is an information bottleneck that worsens with length. Removing exactly that bottleneck is what additive attention, and ultimately the transformer, were invented to do."

build_steps:
  - { title: "1. Tokenize both sides", detail: "Turn the source and target sentences into sequences of tokens (words or subwords), each mapped to a learned embedding vector." }
  - { title: "2. Encode the source", detail: "Run the source embeddings through the encoder LSTM one step at a time. Its evolving hidden state accumulates the meaning of the sentence read so far." }
  - { title: "3. Form the context vector", detail: "Take the encoder's final hidden state as a single fixed-length vector — the compressed summary of the entire source sentence that the decoder will start from." }
  - { title: "4. Prime the decoder", detail: "Initialize the decoder LSTM with the context vector and a start-of-sequence token. The decoder is a separate network with its own weights." }
  - { title: "5. Decode autoregressively", detail: "At each step the decoder predicts the next target token, then feeds that token back in as its next input. During training it is fed the true previous token instead — teacher forcing — to keep learning stable." }
  - { title: "6. Stop at end-of-sequence", detail: "Keep generating until the decoder emits the end-of-sequence token. The emitted tokens, in order, are the translation. Train the whole pair end-to-end by maximizing the probability of the correct target." }

key_equation:
  latex: "p(y_1,\\dots,y_T \\mid x_1,\\dots,x_S) = \\prod_{t=1}^{T} p\\!\\left(y_t \\mid c, y_1,\\dots,y_{t-1}\\right)"
  plain: "The probability of the whole output sentence, given the whole input sentence, is the product of the probabilities of each output word in turn — where every word is predicted from the single context vector c and the words already generated. In short: encode the input into c, then generate the output one conditional word at a time."
  terms:
    - { symbol: "x_1…x_S", meaning: "the source sequence of length S — the input sentence" }
    - { symbol: "y_1…y_T", meaning: "the target sequence of length T — the output sentence, a different length" }
    - { symbol: "c", meaning: "the context vector — the encoder's final state, the compressed meaning of the source" }
    - { symbol: "p(y_t | …)", meaning: "the decoder's predicted probability for the next word given c and prior words" }
    - { symbol: "∏", meaning: "product over all output steps — decoding is one conditional prediction after another" }

strengths:
  - "Maps variable-length inputs to variable-length outputs — the missing piece for translation."
  - "Fully end-to-end: trained on raw sentence pairs, no hand-built alignment or phrase tables."
  - "One clean, general framing that also fits summarization, dialogue, and speech transcription."
  - "Autoregressive decoding produces fluent, context-aware output one token at a time."

weaknesses:
  - "The single fixed-length context vector is a bottleneck — long sentences lose information."
  - "Translation quality degrades as input length grows, needing hacks like reversing the source."
  - "Inherits the RNN's sequential, non-parallel computation over both encoder and decoder."
  - "Errors compound during decoding, since each generated token feeds the next (exposure bias)."

diagram: |
  flowchart LR
    S["Source tokens"] --> ENC["Encoder RNN"]
    ENC --> C["Context vector c"]
    C --> DEC["Decoder RNN"]
    DEC --> T["Target tokens"]
    T -.->|"feed each output back"| DEC
diagram_caption: "The encoder folds the whole source into one context vector; the decoder unrolls it into the target, feeding each generated token back as the next input."

notable_models:
  - { name: "Sutskever seq2seq LSTM", year: 2014, note: "The original — proved a single end-to-end neural net rivals classic phrase-based machine translation." }
  - { name: "Google Neural Machine Translation (GNMT)", year: 2016, note: "Scaled deep seq2seq with attention to production, proving neural translation beats statistical systems at scale." }
  - { name: "Bahdanau attention seq2seq", year: 2015, note: "Added attention over encoder states — proved removing the fixed-vector bottleneck sharply improves long-sentence translation." }

resources:
  - { kind: paper, title: "Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals & Le, 2014)", url: "https://arxiv.org/abs/1409.3215" }
  - { kind: blog, title: "Visualizing A Neural Machine Translation Model — Seq2seq with Attention (Jay Alammar)", url: "https://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/" }
  - { kind: tutorial, title: "NLP From Scratch: Translation with a Sequence-to-Sequence Network (PyTorch)", url: "https://pytorch.org/tutorials/intermediate/seq2seq_translation_tutorial.html" }
  - { kind: course, title: "Sequence Models and Attention (Stanford CS224n)", url: "https://web.stanford.edu/class/cs224n/" }

learn_first:
  - { label: "Recurrent networks & LSTM", href: "/models/architecture/rnn-lstm", tag: "Architecture" }
  - { label: "Word embeddings", href: "/learn/concepts/word-embeddings", tag: "Concept" }
  - { label: "Autoregressive decoding", href: "/learn/concepts/autoregressive-decoding", tag: "Concept" }

tags: [seq2seq, encoder-decoder, translation, classical-dl, foundational]
updated: 2026-07-20
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
