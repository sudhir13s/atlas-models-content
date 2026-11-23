---
id: architecture-era/seq2seq
type: architecture-era
slug: seq2seq
schema_version: 2.5
title: "Sequence-to-Sequence Learning"
name: "Sequence-to-Sequence (Encoder–Decoder)"
era: foundation
era_range: "2014–2016"
year: 2014
family: classical-dl
core_innovation: "An encoder RNN compresses a whole input sequence into a fixed vector that a decoder RNN unrolls into an output sequence."
description: "Two recurrent neural networks that turn one sequence into another, such as a sentence into its translation: the first reads the whole input into one fixed-size list of numbers, and the second writes the output one word at a time from that list."
key_features: ["Encoder–decoder RNN pair", "Fixed-length context vector", "Variable-length input to variable-length output", "End-to-end training on sentence pairs"]
key_models: ["RNN Encoder–Decoder (Cho et al., 2014)", "Sutskever, Vinyals & Le LSTM (2014)"]
paper_title: "Sequence to Sequence Learning with Neural Networks"
paper_url: "https://arxiv.org/abs/1409.3215"
influenced_by: [architecture-era/rnn-lstm]
led_to: [architecture-era/attention-additive]

analogy:
  title: "An interpreter who listens to the whole sentence, then speaks"
  body: "This is an analogy, not the mechanism. A conference interpreter can wait until the speaker finishes a sentence, hold its gist in mind, and then say it in the other language one phrase at a time. The encoder is the listening half: it folds the whole source sentence into one gist, the vector v. The decoder is the speaking half: it turns that gist into the target sentence word by word, each word chosen in light of the words already said. Unlike a person, the network's gist is a list of numbers of one fixed size (8,000 numbers in the paper's model), the same for a five-word sentence as for a fifty-word one, and the decoder cannot go back to the source words."

narrative:
  motivation: "**Neural networks needed inputs and outputs of a fixed size.** The paper is by **Sutskever, Vinyals and Le (2014)** at Google.\n\n- **The limit:** the authors write that deep neural networks can only be applied to problems whose inputs and targets can be encoded as vectors of **fixed dimensionality**.\n- **Why it matters:** in translation, speech recognition and question answering the lengths are **not known in advance**, and a translation need not be as long as its source.\n- **Where neural networks stood:** in translation their role had been largely limited to **one feature** inside a phrase-based statistical system, or to **re-ranking** its candidate translations (Bahdanau et al., Section 6.2)."
  approach: "**Read the whole input into one vector, then write the output from it.**\n\n- **Encoder:** an LSTM (long short-term memory network, a recurrent network with a memory cell) reads the input **one token at a time**; its **last hidden state** is the fixed-size vector **v**.\n- **Decoder:** a second LSTM, which the authors describe as a recurrent language model whose **initial hidden state is set to v**, predicts the output one token at a time.\n- **End of sentence:** every sentence ends with a special `<EOS>` (end-of-sentence) token, which lets the model define a distribution over outputs of **any length**.\n- **Earlier work:** the authors name **Kalchbrenner and Blunsom (2013)** as the first to map an entire input sentence to a vector. **Cho et al. (2014)** proposed the **RNN Encoder–Decoder**, used to score phrase pairs inside a phrase-based system."
  solution: "**Three choices made it work at scale**, the authors report.\n\n- **Two separate LSTMs:** one for the input and one for the output, which adds parameters at **negligible** computational cost.\n- **Depth:** **four** layers; each added layer reduced perplexity by **nearly 10%**.\n- **Reversed source:** the input words are fed in reverse order (c, b, a instead of a, b, c) while the target is not; test perplexity fell from **5.8 to 4.7** and BLEU rose from **25.9 to 30.6**.\n- **Training and search:** maximise the log probability of the correct translation over **12M** sentence pairs, then translate with a left-to-right **beam search**."
  outcome: "**The first time a pure neural translator beat a phrase-based baseline on a large task by a sizeable margin**, as the authors put it. Results on WMT 2014 English to French, in BLEU (a score of how closely a translation matches reference translations):\n\n- **Direct translation:** **34.81** for an ensemble of **5** reversed LSTMs, against **33.30** for the phrase-based baseline.\n- **Re-ranking:** re-scoring the baseline's **1000** best candidates gave **36.5**, close to the best WMT 2014 result of **37.0**.\n- **Long sentences:** no degradation below **35** words and only a minor one on the longest (the paper's Figure 3).\n\n**What came next:**\n\n- **Cho et al. (2014b):** found that an encoder–decoder's performance **degrades rapidly** as sentence length and the number of unknown words grow.\n- **Bahdanau, Cho and Bengio (2014):** conjectured that the fixed-length vector is a **bottleneck** and let the decoder attend over every source word (additive attention)."

key_equation:
  latex: "p(y_1,\\dots,y_{T'} \\mid x_1,\\dots,x_T)=\\prod_{t=1}^{T'} p\\!\\left(y_t \\mid v, y_1,\\dots,y_{t-1}\\right)"
  plain: "In words: the probability of a whole output sentence, given the input sentence, is the probability of its first word, times that of its second word given the first, and so on to the last. Every factor is computed from the one vector v and the words already produced, and each is a softmax over the target vocabulary. The worked example above multiplies four such factors by hand."
  terms:
    - { symbol: "x_1,\\dots,x_T", meaning: "the input sentence, T tokens long" }
    - { symbol: "y_1,\\dots,y_{T'}", meaning: "the output sentence, T′ tokens long and ending with <EOS>; T′ may differ from T" }
    - { symbol: "v", meaning: "the fixed-size vector: the encoder's last hidden state (8,000 numbers in the paper's model)" }
    - { symbol: "p(y_t \\mid v, y_1,\\dots,y_{t-1})", meaning: "the decoder's probability for output word t: one entry of a softmax over the 80,000-word target vocabulary" }
    - { symbol: "\\prod", meaning: "multiply the factors for t = 1 up to T′" }

strengths:
  - "**Any length in, any length out:** the output length T′ may differ from the input length T, and the `<EOS>` token lets the model define a distribution over sequences of **all possible lengths**."
  - "**Beat the phrase-based baseline on its own:** an ensemble of 5 LSTMs scored **34.81** BLEU against **33.30**, translating directly with no phrase-based system involved."
  - "**Few assumptions:** the authors describe a general end-to-end approach that makes **minimal assumptions** on the sequence structure."
  - "**Small memory:** Cho et al. (2014b) report that their neural translation models need only **500MB** in total, against the **tens of gigabytes** existing statistical systems often require."
  - "**Meaningful sentence vectors:** in a 2-D projection of v, phrases cluster by meaning; the authors find v **sensitive to word order** and fairly insensitive to active versus passive voice (the paper's Figure 2)."

weaknesses:
  - "**One fixed-size vector for the whole input:** Cho et al. (2014b) find performance **degrades rapidly** as sentences get longer, and hypothesise that the vector lacks the capacity for a long, complicated sentence; Bahdanau et al. conjecture it is a **bottleneck**."
  - "**Limited vocabulary:** the paper keeps **80,000** target words and replaces the rest with UNK, so its BLEU score is penalised whenever a reference contains a word outside them."
  - "**Slow to train:** about **1,700** words per second on one GPU; the authors spread the model over **8 GPUs** to reach **6,300** words per second, and training took about **ten days**."
  - "**One step after another:** the Transformer authors note that recurrent models compute position by position, which **precludes parallelization** within a training example."

visual:
  kind: distribution
  tokens: ["le", "chat", "noir", "<EOS>"]
  logits: [2.0, 1.0, 0.0, -1.0]
  temperatures: [1]
  panel_titles: ["Decoder's first output step: probability of each candidate word"]
  caption: "The decoder's probabilities for the first word of the translation of \"the black cat\", computed with a softmax from the example scores 2.0, 1.0, 0.0 and −1.0 of the worked example. The paper's model scores all 80,000 words of its target vocabulary. The scores are an example, not from a trained model."

comparison:
  before: "Phrase-based statistical system"
  after: "Sequence-to-sequence LSTM"
  rows:
    - { aspect: "How the translator is built", before: "Many small sub-components, each tuned separately", after: "One large neural network, trained as a whole to maximise the probability of a correct translation", source: "https://arxiv.org/abs/1409.0473" }
    - { aspect: "Role of neural networks", before: "Largely limited to one extra feature, or to re-ranking candidate translations", after: "The network produces the translation on its own", source: "https://arxiv.org/abs/1409.0473" }
    - { aspect: "Vocabulary", before: "Unlimited", after: "80,000 target words; any other word becomes UNK", source: "https://arxiv.org/abs/1409.3215" }
    - { aspect: "BLEU, WMT 2014 English to French test set", before: "33.30 (the baseline)", after: "34.81 (5 reversed LSTMs, beam of 12); 30.59 for one reversed LSTM", source: "https://arxiv.org/abs/1409.3215" }
    - { aspect: "Memory needed", before: "Often tens of gigabytes", after: "About 500MB in total for the models of Cho et al. (2014b)", source: "https://arxiv.org/abs/1409.1259" }

key_numbers:
  columns: ["Setting", "Sutskever, Vinyals and Le (2014)"]
  rows:
    - { cells: ["LSTMs", "2: one encoder, one decoder"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["Layers in each LSTM", "4"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["Cells in each layer", "1,000"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["Numbers in each word vector", "1,000"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["Numbers that represent a sentence (v)", "8,000"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["Vocabulary", "160,000 source words, 80,000 target words"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["Parameters", "384 million, of which 64 million are recurrent connections (32 million per LSTM)"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["Training data", "12 million sentence pairs: 348 million French and 304 million English words"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["Training", "7.5 epochs, batches of 128 sequences, learning rate 0.7, gradient norm capped at 5"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["Hardware and time", "8 GPUs, 6,300 words per second, about ten days"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["BLEU, one reversed LSTM, beam of 12", "30.59"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["BLEU, ensemble of 5 reversed LSTMs, beam of 12", "34.81"], source: "https://arxiv.org/abs/1409.3215" }
    - { cells: ["BLEU, re-ranking the baseline's 1000 best with 5 LSTMs", "36.5"], source: "https://arxiv.org/abs/1409.3215" }

misunderstandings:
  - misunderstanding: "Sutskever, Vinyals and Le invented the encoder–decoder."
    truth: "Their paper names **Kalchbrenner and Blunsom (2013)** as the first to map an entire input sentence to a vector, using convolutional networks, and calls its approach related to **Cho et al. (2014)**, whose RNN Encoder–Decoder worked **inside a phrase-based system** rather than translating on its own. What Sutskever et al. add is a large **deep LSTM** that translates directly; they call the **reversed source** one of the key technical contributions of their work."
    source: "https://arxiv.org/abs/1409.3215"
  - misunderstanding: "Sequence-to-sequence models always failed on long sentences."
    truth: "Cho et al. (2014b) did find rapid degradation with length. But Sutskever et al. report that their LSTM, trained on **reversed** sources, showed **no degradation** on sentences under **35** words and only a **minor** one on the longest. They write that they had expected it to fail because of its limited memory."
    source: "https://arxiv.org/abs/1409.3215"
  - misunderstanding: "Reversing the source helps because it brings the words closer to their translations on average."
    truth: "The authors point out that the **average** distance between corresponding words is **unchanged**; what changes is that the **first** source words end up very close to the first target words, which shortens the minimal time lag. They add that they **do not have a complete explanation**. The effect is large: test perplexity **5.8 to 4.7**, BLEU **25.9 to 30.6**."
    source: "https://arxiv.org/abs/1409.3215"
  - misunderstanding: "The decoder simply outputs the most likely word at each step."
    truth: "The paper searches with a left-to-right **beam search** that keeps the **B** most likely partial translations. The model does well even with a beam of **1** (one candidate kept), and a beam of **2** gives most of the benefit: **33.00**, **34.50** and **34.81** BLEU for beams of 1, 2 and 12 with 5 LSTMs (Table 1)."
    source: "https://arxiv.org/abs/1409.3215"

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
  - { name: "RNN Encoder–Decoder (Cho et al.)", year: 2014, note: "Two recurrent networks with gated hidden units, one encoding a phrase into a fixed-length vector and one decoding it, used to score phrase pairs inside a phrase-based system: test BLEU rose from 33.30 to 33.87 on WMT 2014 English to French." }
  - { name: "Sutskever, Vinyals and Le LSTM", year: 2014, note: "Two 4-layer LSTMs with the source reversed; the authors report 34.81 BLEU on WMT 2014 English to French for an ensemble of 5, against 33.30 for a phrase-based baseline." }
  - { name: "RNNsearch (Bahdanau, Cho and Bengio)", year: 2014, note: "Added a soft search over every encoder state; the authors report that RNNsearch-50 shows no deterioration even on sentences of 50 or more words." }
  - { name: "Google Neural Machine Translation (GNMT)", year: 2016, note: "8 encoder and 8 decoder LSTM layers with residual connections and attention; in a human side-by-side evaluation on isolated simple sentences, translation errors fell by an average of 60% against Google's phrase-based production system." }

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
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/1409.3215", title: "Sequence to Sequence Learning with Neural Networks (Sutskever, Vinyals and Le, 2014)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1406.1078", title: "Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation (Cho et al., 2014)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.1259", title: "On the Properties of Neural Machine Translation: Encoder-Decoder Approaches (Cho et al., 2014b)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.0473", title: "Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho and Bengio, 2014)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1609.08144", title: "Google's Neural Machine Translation System: Bridging the Gap between Human and Machine Translation (Wu et al., 2016)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
---

## What Changed

Until then, Bahdanau et al. write, neural networks in translation mostly supplied **one feature**
to a phrase-based statistical system or **re-ranked** its candidates. Sequence-to-sequence let one
network produce the translation by itself.

How to read the table above:

- **A phrase-based system** translates with many separately tuned parts (phrase tables, a language
  model and so on); the sequence-to-sequence model is **one network** trained end to end on
  sentence pairs.
- **BLEU** scores how closely a machine translation matches human reference translations; higher
  is better. Both systems are scored on the same WMT 2014 English-to-French test set.
- **The vocabulary row** is the price: the network knows 80,000 target words, and the authors note
  its score was penalised whenever a reference translation used another word.
- **The memory row** comes from Cho et al. (2014b): their neural models needed about 500MB, while
  statistical systems often need tens of gigabytes.

## Key Ideas

- **Encoder–decoder split:** one network reads the input, a separate network writes the output, so
  the two lengths can differ.
- **One fixed-size vector:** the encoder's last hidden state, v, is the only thing the decoder
  receives from the input, whatever the input's length.
- **One word at a time:** each output word is a softmax over the vocabulary, and the chosen word is
  fed back as the next input until `<EOS>`.
- **Reversed source:** feeding the input backwards puts the first source words next to the first
  target words, which the authors found made optimisation much easier.

## How It Works

The figure above has **two halves**. The encoder (left) reads the source sentence; the decoder
(right) writes the target sentence. Follow the arrows from left to right.

A **token** is a word or symbol. A **vector** is a list of numbers. An **LSTM** (long short-term
memory network) is a recurrent network: it reads one token per step and carries a **hidden state**
from step to step, with a memory cell that helps it keep information over long gaps.

1. **Tokens in (A, B, C, `<EOS>`).** Every source sentence ends with the `<EOS>` (end-of-sentence)
   token. Each word
   becomes a learned vector of **1,000** numbers; the source vocabulary holds **160,000** words and
   any other word becomes **UNK**.
2. **Encoder LSTM, one step per token.** Each box updates the hidden state from the previous state
   and the current word. The outputs of the individual steps are **thrown away**; only the state
   after the last token is kept. The paper feeds the words in **reverse order** (step 2 of the
   worked example shows why).
3. **The vector v (the small stack of squares).** The final hidden state is the fixed-size vector
   v. In the paper's four-layer model it holds **8,000** numbers, the same for any sentence length.
   This single vector is the **only link** between the two halves.
4. **Decoder LSTM starts from v.** The decoder is a separate LSTM with its own weights; its initial
   hidden state is set to v. In the figure the step that reads `<EOS>` emits the first target word, W.
5. **Softmax over the vocabulary.** At every step the decoder's state gives one score per target
   word (**80,000** of them), and a softmax turns the scores into probabilities that add up to 1
   (step 3 of the worked example).
6. **Feed the word back (the dashed lines).** The chosen word becomes the decoder's next input, and
   the decoder steps again, until it emits `<EOS>`.
7. **Search for a good sentence.** Rather than keeping only the single best word, the paper keeps
   the **B** most likely partial translations at every step (beam search). A beam of 1 already does
   well and a beam of 2 gives most of the benefit.
8. **Training.** The model maximises the log probability of the correct translation over the
   training pairs. By the key equation, that is the sum of the log probabilities of each correct
   word given v and the correct words before it (step 4 of the worked example).

## Worked Example

**Translating "the black cat" into "le chat noir", by hand.** All scores and probabilities are an
example, not from a trained model; they are small so every step can be checked.

**Step 1: the vector v has one size.** In the paper's model v holds **8,000** numbers. A
3-word sentence and a 30-word sentence both end as 8,000 numbers, so a longer sentence has less room
per word.

**Step 2: why the source is reversed.** Take the paper's own example: source a, b, c translates to
α, β, γ, with a ↔ α, b ↔ β, c ↔ γ. Write the source followed by the target and number the positions.

- **Normal order** a b c α β γ: a is at 1 and α at 4, distance **3**; b ↔ β: 5 − 2 = **3**;
  c ↔ γ: 6 − 3 = **3**. Mean: (3 + 3 + 3) / 3 = **3**.
- **Reversed** c b a α β γ: a is at 3 and α at 4, distance **1**; b ↔ β: 5 − 2 = **3**;
  c ↔ γ: 6 − 1 = **5**. Mean: (1 + 3 + 5) / 3 = **3**.
- The mean is **unchanged**, but the first word pair is now only **1** step apart, which is the
  effect the authors credit.

**Step 3: the first output word** (what the figure draws). Starting from v, the decoder scores four
candidate words: le **2.0**, chat **1.0**, noir **0.0**, `<EOS>` **−1.0**.

1. $e^{2.0} \approx 7.389$, $e^{1.0} \approx 2.718$, $e^{0.0} = 1$ and $e^{-1.0} \approx 0.368$;
   the total is 11.475.
2. Probabilities: le 7.389 / 11.475 ≈ **0.644**, chat **0.237**, noir **0.087**, `<EOS>` **0.032**.
   They add up to 1.

**Step 4: the whole sentence.** "le" is fed back and the decoder repeats step 3 three more times.
Suppose it gives chat **0.70** after "le", noir **0.60** after "le chat", and `<EOS>` **0.90** after
"le chat noir".

1. By the key equation, the sentence's probability is the product:
   0.644 × 0.70 = 0.4508; × 0.60 = 0.2705; × 0.90 ≈ **0.243**.
2. Training works with logarithms: $\ln 0.644 \approx -0.440$, $\ln 0.70 \approx -0.357$,
   $\ln 0.60 \approx -0.511$, $\ln 0.90 \approx -0.105$; the sum is **−1.413**, which is
   $\ln 0.243$.
3. Training raises this log probability, here by making each of the four correct words more likely.

**What this means:** everything the decoder knows about "the black cat" has to pass through v, and
each output word is one softmax over the vocabulary, chosen given v and the words already written.
The sentence's probability is the product of those word probabilities, so one unlikely word pulls
down the whole sentence.
