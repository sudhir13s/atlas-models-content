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
key_features: ["Encoder–decoder RNN pair", "Fixed-length context vector", "Variable-length input to variable-length output", "End-to-end training on sentence pairs"]
key_models: ["RNN Encoder–Decoder (Cho et al., 2014)", "Sutskever, Vinyals & Le LSTM (2014)"]
paper_title: "Sequence to Sequence Learning with Neural Networks"
paper_url: "https://arxiv.org/abs/1409.3215"
influenced_by: [architecture-era/rnn-lstm]
led_to: [architecture-era/attention-additive]

eli5: "To translate a sentence, one network reads the whole English sentence and squeezes its meaning into a single summary. A second network takes that summary and writes out the French sentence one word at a time. The first network is the 'reader', the second is the 'writer', and the summary is the handoff between them."

analogy:
  title: "An interpreter who listens fully, then speaks"
  body: "Think of a human interpreter at a conference. They don't translate word-for-word as you speak; they *listen to your entire sentence*, form a compact mental gist of what you meant, and only then turn to the other side and render it in the target language, phrase by phrase. Sequence-to-sequence works the same way: the encoder is the listening half that folds the whole source sentence into one mental gist (the context vector), and the decoder is the speaking half that unrolls that gist into the target sentence, one word at a time, each word informed by the words it just said. The single gist has a fixed size, whatever the length of the sentence."

narrative:
  motivation: "Sutskever, Vinyals and Le note that deep neural networks, although powerful, could not be used to map sequences to sequences. Translation is not length-preserving: a five-word English sentence might become a seven-word French one, and the word order shuffles."
  approach: "Cho et al. (RNN Encoder–Decoder) and Sutskever, Vinyals and Le (2014) split the job in two. In Sutskever et al.'s version, one LSTM, the encoder, reads the entire source sequence token by token and ends with a hidden state that summarizes the whole input. A second LSTM, the decoder, starts from that summary and generates the target sequence one token at a time, each new token conditioned on the summary and on the tokens it has already produced."
  solution: "The encoder's final hidden state becomes a single fixed-length 'context vector' — the entire meaning of the source, compressed. The decoder is autoregressive: it emits a word, feeds that word back as its next input, and repeats until it produces an end-of-sentence marker. The whole thing trains end-to-end on sentence pairs by maximizing the probability the decoder assigns to the correct target given the true previous tokens (teacher forcing)."
  outcome: "On WMT'14 English-to-French, Sutskever et al. report 34.8 BLEU against 33.3 for a phrase-based statistical system, and that reversing the source sentence markedly improved results; with it, the LSTM did not have difficulty on long sentences. Cho et al. found that their basic encoder–decoder degrades rapidly as sentence length grows, and Bahdanau, Cho and Bengio conjectured that the fixed-length vector is a bottleneck, which additive attention removes."

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
  - "End-to-end: trained on sentence pairs, with no hand-built alignment or phrase tables."
  - "The authors describe it as a general approach that makes minimal assumptions on the sequence structure."
  - "Autoregressive decoding generates the output one token at a time, each conditioned on those before."

weaknesses:
  - "The single fixed-length context vector must hold the whole source; Bahdanau, Cho and Bengio conjecture it is a bottleneck."
  - "Cho et al. report that a basic encoder–decoder degrades rapidly as sentence length and the number of unknown words increase."
  - "Inherits the RNN's sequential, non-parallel computation over both encoder and decoder."
  - "Errors compound during decoding, since each generated token feeds the next (exposure bias)."

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 870 480" role="img" aria-label="An encoder LSTM reads the source tokens A, B, C, end-of-sentence one step at a time; its final state, a single fixed-size vector v, is the only link to a separate decoder LSTM, which emits W, X, Y, Z, end-of-sentence, feeding each output back as its next input.">
  <defs><marker id="seq2seq-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="seq2seq-arrow-strong" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="seq2seq-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <rect class="d-frame" x="30" y="60" width="310" height="315" rx="8"/>
  <rect class="d-frame" x="440" y="60" width="400" height="315" rx="8"/>
  <text class="d-title" x="185" y="82" text-anchor="middle">Encoder LSTM: reads the source</text>
  <text class="d-title" x="640" y="82" text-anchor="middle">Decoder LSTM: writes the target</text>
  <text class="d-note" x="185" y="165" text-anchor="middle">per-step outputs are discarded;</text>
  <text class="d-note" x="185" y="179" text-anchor="middle">only the final state is kept</text>
  <rect class="d-box d-role-input" x="47" y="319" width="46" height="22" rx="4"/>
  <text class="d-label" x="70" y="334" text-anchor="middle">A</text>
  <path class="d-line-strong" d="M70 319 V264" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-feedforward" x="45" y="218" width="50" height="44" rx="5"/>
  <text class="d-label" x="70" y="244" text-anchor="middle">LSTM</text>
  <path class="d-line-strong" d="M95 240 H123" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-input" x="127" y="319" width="46" height="22" rx="4"/>
  <text class="d-label" x="150" y="334" text-anchor="middle">B</text>
  <path class="d-line-strong" d="M150 319 V264" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-feedforward" x="125" y="218" width="50" height="44" rx="5"/>
  <text class="d-label" x="150" y="244" text-anchor="middle">LSTM</text>
  <path class="d-line-strong" d="M175 240 H203" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-input" x="207" y="319" width="46" height="22" rx="4"/>
  <text class="d-label" x="230" y="334" text-anchor="middle">C</text>
  <path class="d-line-strong" d="M230 319 V264" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-feedforward" x="205" y="218" width="50" height="44" rx="5"/>
  <text class="d-label" x="230" y="244" text-anchor="middle">LSTM</text>
  <path class="d-line-strong" d="M255 240 H283" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-input" x="287" y="319" width="46" height="22" rx="4"/>
  <text class="d-label" x="310" y="334" text-anchor="middle">&lt;EOS&gt;</text>
  <path class="d-line-strong" d="M310 319 V264" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-feedforward" x="285" y="218" width="50" height="44" rx="5"/>
  <text class="d-label" x="310" y="244" text-anchor="middle">LSTM</text>
  <rect class="d-box d-role-input" x="457" y="319" width="46" height="22" rx="4"/>
  <text class="d-label" x="480" y="334" text-anchor="middle">&lt;EOS&gt;</text>
  <path class="d-line-strong" d="M480 319 V264" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-feedforward" x="455" y="218" width="50" height="44" rx="5"/>
  <text class="d-label" x="480" y="244" text-anchor="middle">LSTM</text>
  <path class="d-line-strong" d="M505 240 H533" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-input" x="537" y="319" width="46" height="22" rx="4"/>
  <text class="d-label" x="560" y="334" text-anchor="middle">W</text>
  <path class="d-line-strong" d="M560 319 V264" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-feedforward" x="535" y="218" width="50" height="44" rx="5"/>
  <text class="d-label" x="560" y="244" text-anchor="middle">LSTM</text>
  <path class="d-line-strong" d="M585 240 H613" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-input" x="617" y="319" width="46" height="22" rx="4"/>
  <text class="d-label" x="640" y="334" text-anchor="middle">X</text>
  <path class="d-line-strong" d="M640 319 V264" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-feedforward" x="615" y="218" width="50" height="44" rx="5"/>
  <text class="d-label" x="640" y="244" text-anchor="middle">LSTM</text>
  <path class="d-line-strong" d="M665 240 H693" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-input" x="697" y="319" width="46" height="22" rx="4"/>
  <text class="d-label" x="720" y="334" text-anchor="middle">Y</text>
  <path class="d-line-strong" d="M720 319 V264" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-feedforward" x="695" y="218" width="50" height="44" rx="5"/>
  <text class="d-label" x="720" y="244" text-anchor="middle">LSTM</text>
  <path class="d-line-strong" d="M745 240 H773" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-input" x="777" y="319" width="46" height="22" rx="4"/>
  <text class="d-label" x="800" y="334" text-anchor="middle">Z</text>
  <path class="d-line-strong" d="M800 319 V264" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-feedforward" x="775" y="218" width="50" height="44" rx="5"/>
  <text class="d-label" x="800" y="244" text-anchor="middle">LSTM</text>
  <path class="d-line-strong" d="M480 218 V123" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-output" x="457" y="99" width="46" height="22" rx="4"/>
  <text class="d-label" x="480" y="114" text-anchor="middle">W</text>
  <path class="d-line-back" d="M503 110 H520 V330 H535" marker-end="url(#seq2seq-arrow)"/>
  <path class="d-line-strong" d="M560 218 V123" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-output" x="537" y="99" width="46" height="22" rx="4"/>
  <text class="d-label" x="560" y="114" text-anchor="middle">X</text>
  <path class="d-line-back" d="M583 110 H600 V330 H615" marker-end="url(#seq2seq-arrow)"/>
  <path class="d-line-strong" d="M640 218 V123" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-output" x="617" y="99" width="46" height="22" rx="4"/>
  <text class="d-label" x="640" y="114" text-anchor="middle">Y</text>
  <path class="d-line-back" d="M663 110 H680 V330 H695" marker-end="url(#seq2seq-arrow)"/>
  <path class="d-line-strong" d="M720 218 V123" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-output" x="697" y="99" width="46" height="22" rx="4"/>
  <text class="d-label" x="720" y="114" text-anchor="middle">Z</text>
  <path class="d-line-back" d="M743 110 H760 V330 H775" marker-end="url(#seq2seq-arrow)"/>
  <path class="d-line-strong" d="M800 218 V123" marker-end="url(#seq2seq-arrow-strong)"/>
  <rect class="d-box d-role-output" x="777" y="99" width="46" height="22" rx="4"/>
  <text class="d-label" x="800" y="114" text-anchor="middle">&lt;EOS&gt;</text>
  <text class="d-note" x="640" y="362" text-anchor="middle">dashed: each emitted token becomes the next input</text>
  <rect class="d-box d-role-attention" x="385.5" y="194.5" width="13" height="13" rx="1"/>
  <rect class="d-box d-role-attention" x="385.5" y="207.5" width="13" height="13" rx="1"/>
  <rect class="d-box d-role-attention" x="385.5" y="220.5" width="13" height="13" rx="1"/>
  <rect class="d-box d-role-attention" x="385.5" y="233.5" width="13" height="13" rx="1"/>
  <rect class="d-box d-role-attention" x="385.5" y="246.5" width="13" height="13" rx="1"/>
  <rect class="d-box d-role-attention" x="385.5" y="259.5" width="13" height="13" rx="1"/>
  <rect class="d-box d-role-attention" x="385.5" y="272.5" width="13" height="13" rx="1"/>
  <path class="d-line-accent" d="M335 240 H382.5" marker-end="url(#seq2seq-arrow-accent)"/>
  <path class="d-line-accent" d="M398.5 240 H453" marker-end="url(#seq2seq-arrow-accent)"/>
  <text class="d-title" x="392" y="182.5" text-anchor="middle">v</text>
  <text class="d-note" x="392" y="301.5" text-anchor="middle">fixed-size</text>
  <text class="d-note" x="392" y="314.5" text-anchor="middle">vector</text>
  <text class="d-math" x="30" y="412" text-anchor="start">p(y₁ … yₘ | x₁ … xₙ) = ∏ₜ p(yₜ | v, y₁ … yₜ₋₁)</text>
  <text class="d-note" x="30" y="434" text-anchor="start">v = the encoder's last hidden state: the whole source must fit in this one vector, whatever its length.</text>
  <text class="d-note" x="30" y="450" text-anchor="start">Paper (Sutskever, Vinyals &amp; Le 2014): 4 stacked LSTM layers of 1000 cells, 1000-d word embeddings; one layer drawn.</text>
  <text class="d-note" x="30" y="466" text-anchor="start">The paper feeds the source in reverse (C, B, A), which shortens the gap between the first source and first target words.</text>
  </svg>
diagram_caption: "An encoder LSTM reads A B C <EOS> and keeps only its final state v; that one fixed-size vector is the sole link to a separate decoder LSTM, which emits W X Y Z <EOS>, feeding each output back as its next input."
notable_models:
  - { name: "RNN Encoder–Decoder (Cho et al.)", year: 2014, note: "Two recurrent networks, one encoding a sequence into a fixed-length vector and one decoding it, used to score phrase pairs for statistical translation." }
  - { name: "Sutskever seq2seq LSTM", year: 2014, note: "The authors report 34.8 BLEU on WMT'14 English-to-French against 33.3 for a phrase-based system." }
  - { name: "Bahdanau attention seq2seq", year: 2015, note: "Added a soft search over encoder states; the authors report that RNNsearch-50 shows no deterioration on sentences of 50 or more words." }
  - { name: "Google Neural Machine Translation (GNMT)", year: 2016, note: "8-layer LSTM encoder and decoder with attention and residual connections; human evaluation shows 60% fewer translation errors than Google's phrase-based production system on simple sentences." }

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
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/1409.3215", title: "Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals & Le, 2014)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1406.1078", title: "Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation (Cho et al., arXiv:1406.1078)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.1259", title: "On the Properties of Neural Machine Translation: Encoder-Decoder Approaches (Cho et al., arXiv:1409.1259)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.0473", title: "Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho & Bengio, arXiv:1409.0473)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1609.08144", title: "Google's Neural Machine Translation System (Wu et al., arXiv:1609.08144)", trust: 0.95 }
---

## What Changed
Translation maps a sentence to one of a different length. In 2014 Cho et al. (RNN Encoder–Decoder) and
Sutskever, Vinyals and Le framed this as **sequence-to-sequence**: an encoder reads the source and folds
it into a single fixed-length vector, and a decoder generates the target one token at a time.

Sutskever et al. used a multilayered LSTM encoder and a deep LSTM decoder, trained end-to-end on
sentence pairs. They report 34.8 BLEU on WMT'14 English-to-French against 33.3 for a phrase-based
system, and that reversing the source sentence markedly improved results.

Cho et al. found that a basic encoder–decoder degrades rapidly on long sentences. Bahdanau, Cho and
Bengio conjectured that the fixed-length vector is the bottleneck; additive attention removes it.

## Key Ideas
- **Encoder–decoder split** — one network summarises the input, a separate network generates the output,
  cleanly handling different input and output lengths.
- **Fixed context vector** — the encoder's final hidden state must carry the whole meaning of the
  source, whatever its length.
- **Autoregressive decoding** — the decoder emits one token at a time, feeding each output back as the
  next input (with teacher forcing during training).
- **Reversed source** — Sutskever et al. found that reversing the source words (not the target)
  introduced many short-term dependencies and made optimisation easier.
