---
id: architecture-era/attention-additive
type: architecture-era
slug: attention-additive
schema_version: 3.1
title: Additive Attention
name: Additive (Bahdanau) Attention
era: foundation
era_range: 2014–2017
year: 2014
family: classical-dl
core_innovation: The decoder learns a soft, content-based weighting over all encoder states each step, replacing the single fixed context vector.
description: 'An addition to the encoder–decoder translator: before writing each output word, the decoder scores every input word with a small neural network and reads a weighted blend of them, instead of one fixed summary of the whole sentence.'
key_features:
- Soft alignment over encoder states
- Learned additive scoring network
- Per-step dynamic context vector
- Differentiable, jointly trained
paper_title: Neural Machine Translation by Jointly Learning to Align and Translate
paper_url: https://arxiv.org/abs/1409.0473
influenced_by:
- architecture-era/seq2seq
led_to:
- architecture-era/transformer
analogy:
  title: A soft spotlight over the source sentence
  body: 'This is an analogy, not the mechanism. Picture the source sentence laid out on a table, one word per card. Before writing each word of the translation, the translator sweeps a spotlight over all the cards. The light does not land on one card: it is bright on the words that matter now and dim on the rest, and the brightness levels always add up to the same total. The translator then reads a blend of the cards, weighted by their brightness. Each output word gets a new setting of the spotlight. In the model, the brightness of each card comes from a small learned network that compares the translator''s current state with that word; the exact rule is the key equation below.'
narrative:
  motivation: '**One fixed-length vector had to hold the whole source sentence.** The paper is by **Bahdanau, Cho and Bengio (2014)**, first posted on arXiv in 2014 and published at the International Conference on Learning Representations (ICLR) in 2015.


    - **The encoder–decoder:** in the models of **Cho et al.** and **Sutskever et al. (2014)**, an encoder reads the source into **one fixed-length vector** and a decoder writes the translation from it.

    - **The worry:** the network must compress all the information of a sentence into that vector, which may make long sentences hard, especially ones **longer than those in the training data**.

    - **The evidence:** the authors cite **Cho et al. (2014b)**, who showed that a basic encoder–decoder''s performance **deteriorates rapidly** as the input sentence gets longer.'
  approach: '**Keep a vector for every source word, and let the decoder search them.**


    - **The conjecture:** the fixed-length vector is a **bottleneck**.

    - **The idea:** each time the model writes a word, it **(soft-)searches** for the source positions where the most relevant information is concentrated.

    - **Soft, not hard:** no position is picked outright; every position gets a weight, so gradients flow through the search and it is **trained jointly** with the rest of the translator.'
  solution: '**A small network scores every source word at every output step.**


    - **Annotations:** a **bidirectional RNN** (recurrent neural network) gives each source word an annotation hⱼ that summarises the words **before and after** it.

    - **Scores:** an **alignment model**, a feed-forward network with one hidden layer, scores each annotation against the decoder''s previous state sᵢ₋₁.

    - **Weights and context:** a **softmax** turns the scores into weights αᵢⱼ that add up to 1, and their weighted sum of annotations is a **context vector cᵢ**, recomputed for **every** output word.

    - **The name:** the paper calls this an alignment model; **Vaswani et al. (2017)** refer to it as **additive attention**, one of the two most used attention functions next to dot-product attention.'
  outcome: '**Long sentences stopped breaking the translator**, the authors report, on WMT 2014 English to French, in BLEU (a score of how closely a translation matches reference translations):


    - **No deterioration:** RNNsearch-50 shows **no performance deterioration** even on sentences of **50 or more** words, while the encoder–decoder''s score drops dramatically with length.

    - **Scores:** **26.75** against **17.82** for the encoder–decoder on all test sentences; on sentences with no unknown words, **36.15** for a longer-trained RNNsearch-50 against **35.63** for the phrase-based system Moses.

    - **Alignments:** plotted weights give a soft word alignment; the model aligns **[zone]** with **[Area]** to translate ''European Economic Area'' as **''zone économique européenne''**.


    **What came next:**


    - **Luong, Pham and Manning (2015):** compared **global** and **local** attention and simpler scoring functions such as the **dot product**.

    - **The Transformer (2017):** an architecture based **solely on attention**, with no recurrence.'
key_equation:
  latex: e_{ij}=v_a^{\top}\tanh\!\left(W_a\,s_{i-1}+U_a\,h_j\right),\qquad \alpha_{ij}=\frac{\exp(e_{ij})}{\sum_{k=1}^{T_x}\exp(e_{ik})},\qquad c_i=\sum_{j=1}^{T_x}\alpha_{ij}\,h_j
  plain: 'In words: to decide how much output word i should draw on source word j, transform the decoder''s previous state and the word''s annotation with two learned matrices, add them, squash each entry with tanh, and collapse the result to one number with the vector v_a. A softmax turns the scores for all source words into weights that add up to 1, and the context vector is the annotations averaged with those weights. The worked example above does all three steps by hand.'
  terms:
  - symbol: e_{ij}
    meaning: the score (the paper calls it the energy) of source word j for output word i
  - symbol: s_{i-1}
    meaning: the decoder's hidden state just before it emits output word i (1,000 numbers in the paper)
  - symbol: h_j
    meaning: 'the annotation of source word j: forward and backward encoder states joined (2,000 numbers in the paper)'
  - symbol: W_a, U_a
    meaning: learned matrices that map the decoder state and the annotation to the alignment model's hidden layer (1,000 units in the paper)
  - symbol: v_a
    meaning: a learned vector that turns the hidden layer into one score
  - symbol: \alpha_{ij}
    meaning: the weight of source word j for output word i; the weights of one output word add up to 1
  - symbol: c_i
    meaning: 'the context vector for output word i: the weighted sum of all annotations'
  - symbol: T_x
    meaning: the number of words in the source sentence
strengths:
- '**Long sentences hold up:** RNNsearch-50 shows **no deterioration** even on sentences of **50 or more** words, and RNNsearch-30 (trained on sentences up to 30 words) beats the encoder–decoder trained up to 50: **21.50** against **17.82** BLEU.'
- '**Large gains over the encoder–decoder:** **26.75** against **17.82** BLEU on all test sentences, and **34.16** against **26.71** on sentences with no unknown words (the paper''s Table 1).'
- '**Close to the phrase-based system:** on sentences with no unknown words, a longer-trained RNNsearch-50 scored **36.15** against **35.63** for Moses, which also used a separate **418M**-word monolingual corpus.'
- '**Alignments you can see:** plotting the weights αᵢⱼ shows which source words each output word drew on, including **non-monotonic** ones such as adjective and noun swaps.'
- '**Learned with the translator:** the alignment is not a hidden variable to be inferred; gradients flow through it, so it is trained **jointly** with the whole model.'
- '**Different phrase lengths:** the authors note that soft alignment handles source and target phrases of different lengths without mapping words to or from **nowhere ([NULL])**.'
weaknesses:
- '**Cost for every pair of words:** the alignment model runs **source length × target length** times per sentence pair; the authors call this not severe for translation''s typical **15 to 40** words but say it may limit other tasks.'
- '**Unknown words remain:** with a **30,000**-word vocabulary, RNNsearch-50 scored **28.45** BLEU on all test sentences against **33.30** for Moses; the authors name rare and unknown words as a challenge left for the future.'
- '**Still recurrent:** the Transformer authors note that such attention was almost always used **together with a recurrent network**, whose sequential nature **precludes parallelization** within a training example.'
- '**Slower than dot-product attention:** the Transformer authors write that the two have similar theoretical complexity, but dot-product attention is **much faster and more space-efficient** in practice because it runs on optimised matrix multiplication.'
visual:
  kind: distribution
  tokens:
  - the
  - black
  - cat
  logits:
  - 0
  - 0.762
  - 1.524
  temperatures:
  - 1
  panel_titles:
  - Attention weights over the three source words
  caption: The weights αᵢⱼ with which the decoder, about to write the French word after "le", draws on the source words "the black cat", computed with a softmax from the example scores 0, 0.762 and 1.524 of the worked example. The numbers are an example, not from a trained model.
comparison:
  before: Encoder–decoder (RNNencdec)
  after: Additive attention (RNNsearch)
  rows:
  - aspect: What the decoder receives from the source
    before: One fixed-length vector c for the whole sentence
    after: 'A new context vector cᵢ for every output word: a weighted sum of all annotations'
    source: https://arxiv.org/abs/1409.0473
  - aspect: What the encoder keeps
    before: A summary of the sentence
    after: One annotation per source word, from forward and backward RNNs
    source: https://arxiv.org/abs/1409.0473
  - aspect: Longer sentences
    before: Performance drops dramatically as sentences get longer
    after: No deterioration even at 50 words or more (RNNsearch-50)
    source: https://arxiv.org/abs/1409.0473
  - aspect: BLEU, all test sentences (models trained on up to 50 words)
    before: '17.82'
    after: '26.75'
    source: https://arxiv.org/abs/1409.0473
  - aspect: BLEU, test sentences with no unknown words
    before: '26.71'
    after: '34.16'
    source: https://arxiv.org/abs/1409.0473
  - aspect: Extra computation
    before: None
    after: The alignment model runs once per source word for every output word
    source: https://arxiv.org/abs/1409.0473
key_numbers:
  columns:
  - Setting
  - RNNencdec
  - RNNsearch
  rows:
  - cells:
    - Encoder
    - 1,000 hidden units
    - Forward and backward RNNs, 1,000 hidden units each
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - Decoder hidden units
    - 1,000
    - 1,000
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - Numbers in each word vector
    - '620'
    - '620'
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - Hidden units in the alignment model
    - No alignment model
    - 1,000
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - Vocabulary in each language
    - 30,000 words
    - 30,000 words
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - Training data
    - 348 million words of sentence pairs
    - 348 million words of sentence pairs
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - Sentences per training batch
    - '80'
    - '80'
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - Training time, models trained on up to 50 words
    - 108 hours
    - 111 hours (252 hours for the longer-trained model)
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - BLEU, all test sentences, trained on up to 30 words
    - '13.93'
    - '21.50'
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - BLEU, all test sentences, trained on up to 50 words
    - '17.82'
    - 26.75 (28.45 longer-trained)
    source: https://arxiv.org/abs/1409.0473
  - cells:
    - BLEU, no unknown words, trained on up to 50 words
    - '26.71'
    - 34.16 (36.15 longer-trained)
    source: https://arxiv.org/abs/1409.0473
misunderstandings:
- misunderstanding: Attention was introduced by the Transformer paper.
  truth: The Transformer paper cites **Bahdanau et al. (2014)** for additive attention and writes that, in all but a few cases, attention had been used **together with a recurrent network**. What the Transformer changed was to rely **entirely** on attention, with no recurrence.
  source: https://arxiv.org/abs/1706.03762
- misunderstanding: Additive attention is just a worse version of dot-product attention.
  truth: The Transformer authors write that the two have **similar theoretical complexity** and perform similarly for small key sizes, and that additive attention **outperforms** dot-product attention **without scaling** for larger key sizes. Dot-product attention wins on **speed and memory**; the Transformer keeps it and divides the dot products by √dₖ to counteract the large-key effect.
  source: https://arxiv.org/abs/1706.03762
- misunderstanding: Attention picks the one source word each output word comes from.
  truth: 'Every source word gets a weight, and the weights add up to 1. The authors show why this helps: to translate [the man] as [l'' homme], a hard one-to-one alignment would map [the] to [l''], but choosing between le, la, les and l'' depends on the **next** word. The soft alignment lets the model look at **both** [the] and [man].'
  source: https://arxiv.org/abs/1409.0473
- misunderstanding: The score compares each source word with the decoder state that writes the current word.
  truth: 'In Bahdanau et al. the score uses the **previous** decoder state sᵢ₋₁, and the new state sᵢ is computed afterwards from the context. Luong, Pham and Manning (2015) describe this path as **sᵢ₋₁ → weights → context → sᵢ** and simplified it: their models score the **current** top-layer state hₜ.'
  source: https://arxiv.org/abs/1508.04025
diagram_svg: '<svg class="atlas-figure" viewBox="0 0 880 515" role="img" aria-label="Additive attention for one decoder step: a bidirectional encoder gives one annotation h_j per source word; the previous decoder state s_(i-1) is scored against every h_j by a small network, a softmax turns the scores into weights alpha_ij, and the weighted sum c_i feeds the new decoder state s_i, which emits y_i.">

  <defs><marker id="attention-additive-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="attention-additive-arrow-strong" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="attention-additive-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>

  <text class="d-title" x="40" y="30" text-anchor="start">One decoder step i attends over every source annotation</text>

  <text class="d-math" x="120" y="455" text-anchor="middle">x₁</text>

  <path class="d-line-strong" d="M120 443 V406" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-input" x="98" y="360" width="44" height="44" rx="4"/>

  <path class="d-line" d="M98 382 H142"/>

  <text class="d-note" x="120" y="375" text-anchor="middle">→h₁</text>

  <text class="d-note" x="120" y="397" text-anchor="middle">←h₁</text>

  <path class="d-line-strong" d="M142 371 H186" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line-strong" d="M188 393 H144" marker-end="url(#attention-additive-arrow-strong)"/>

  <text class="d-math" x="210" y="455" text-anchor="middle">x₂</text>

  <path class="d-line-strong" d="M210 443 V406" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-input" x="188" y="360" width="44" height="44" rx="4"/>

  <path class="d-line" d="M188 382 H232"/>

  <text class="d-note" x="210" y="375" text-anchor="middle">→h₂</text>

  <text class="d-note" x="210" y="397" text-anchor="middle">←h₂</text>

  <path class="d-line-strong" d="M232 371 H276" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line-strong" d="M278 393 H234" marker-end="url(#attention-additive-arrow-strong)"/>

  <text class="d-math" x="300" y="455" text-anchor="middle">x₃</text>

  <path class="d-line-strong" d="M300 443 V406" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-input" x="278" y="360" width="44" height="44" rx="4"/>

  <path class="d-line" d="M278 382 H322"/>

  <text class="d-note" x="300" y="375" text-anchor="middle">→h₃</text>

  <text class="d-note" x="300" y="397" text-anchor="middle">←h₃</text>

  <path class="d-line-strong" d="M322 371 H366" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line-strong" d="M368 393 H324" marker-end="url(#attention-additive-arrow-strong)"/>

  <text class="d-math" x="390" y="455" text-anchor="middle">x₄</text>

  <path class="d-line-strong" d="M390 443 V406" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-input" x="368" y="360" width="44" height="44" rx="4"/>

  <path class="d-line" d="M368 382 H412"/>

  <text class="d-note" x="390" y="375" text-anchor="middle">→h₄</text>

  <text class="d-note" x="390" y="397" text-anchor="middle">←h₄</text>

  <path class="d-line-strong" d="M412 371 H456" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line-strong" d="M458 393 H414" marker-end="url(#attention-additive-arrow-strong)"/>

  <text class="d-math" x="480" y="455" text-anchor="middle">x₅</text>

  <path class="d-line-strong" d="M480 443 V406" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-input" x="458" y="360" width="44" height="44" rx="4"/>

  <path class="d-line" d="M458 382 H502"/>

  <text class="d-note" x="480" y="375" text-anchor="middle">→h₅</text>

  <text class="d-note" x="480" y="397" text-anchor="middle">←h₅</text>

  <text class="d-title" x="545" y="380" text-anchor="start">bidirectional RNN encoder</text>

  <text class="d-math" x="545" y="397" text-anchor="start">annotation h<tspan class="d-note" dy="3">j</tspan><tspan dy="-3"> = [→h</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3"> ; ←h</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3">]</tspan></text>

  <rect class="d-box d-role-output" x="732" y="326" width="96" height="30" rx="5"/>

  <text class="d-math" x="780" y="345" text-anchor="middle">s<tspan class="d-note" dy="3">i−1</tspan><tspan dy="-3"> </tspan></text>

  <path class="d-line-strong" d="M732 341 H96"/>

  <text class="d-note" x="545" y="356" text-anchor="start">s<tspan class="d-note" dy="3">i−1</tspan><tspan dy="-3"> is compared with every h</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3"> </tspan></text>

  <rect class="d-fill-soft" x="86" y="252" width="420" height="22" rx="4"/>

  <path class="d-line" d="M96 341 V324" marker-end="url(#attention-additive-arrow)"/>

  <path class="d-line-strong" d="M108 360 V324" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-feedforward" x="90" y="298" width="36" height="24" rx="4"/>

  <text class="d-math" x="108" y="313" text-anchor="middle">e<tspan class="d-note" dy="3">i1</tspan><tspan dy="-3"> </tspan></text>

  <path class="d-line-strong" d="M108 298 V276" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line-strong" d="M108 252 V240"/>

  <rect class="d-box d-role-attention" x="100" y="232.3" width="16" height="7.700000000000001" rx="1"/>

  <text class="d-note" x="108" y="226.3" text-anchor="middle">0.07</text>

  <path class="d-line-strong" d="M108 214.3 V161" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line" d="M136 360 V150 H119" marker-end="url(#attention-additive-arrow)"/>

  <circle class="d-node" cx="108" cy="150" r="9"/>

  <text class="d-label" x="108" y="154" text-anchor="middle">×</text>

  <path class="d-line-accent" d="M108 141 L283.5 99.0" marker-end="url(#attention-additive-arrow-accent)" opacity="0.31"/>

  <path class="d-line" d="M186 341 V324" marker-end="url(#attention-additive-arrow)"/>

  <path class="d-line-strong" d="M198 360 V324" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-feedforward" x="180" y="298" width="36" height="24" rx="4"/>

  <text class="d-math" x="198" y="313" text-anchor="middle">e<tspan class="d-note" dy="3">i2</tspan><tspan dy="-3"> </tspan></text>

  <path class="d-line-strong" d="M198 298 V276" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line-strong" d="M198 252 V240"/>

  <rect class="d-box d-role-attention" x="190" y="226.8" width="16" height="13.2" rx="1"/>

  <text class="d-note" x="198" y="220.8" text-anchor="middle">0.12</text>

  <path class="d-line-strong" d="M198 208.8 V161" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line" d="M226 360 V150 H209" marker-end="url(#attention-additive-arrow)"/>

  <circle class="d-node" cx="198" cy="150" r="9"/>

  <text class="d-label" x="198" y="154" text-anchor="middle">×</text>

  <path class="d-line-accent" d="M198 141 L284.5 102.0" marker-end="url(#attention-additive-arrow-accent)" opacity="0.39"/>

  <path class="d-line" d="M276 341 V324" marker-end="url(#attention-additive-arrow)"/>

  <path class="d-line-strong" d="M288 360 V324" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-feedforward" x="270" y="298" width="36" height="24" rx="4"/>

  <text class="d-math" x="288" y="313" text-anchor="middle">e<tspan class="d-note" dy="3">i3</tspan><tspan dy="-3"> </tspan></text>

  <path class="d-line-strong" d="M288 298 V276" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line-strong" d="M288 252 V240"/>

  <rect class="d-box d-role-attention" x="280" y="185" width="16" height="55" rx="1"/>

  <text class="d-note" x="288" y="179" text-anchor="middle">0.50</text>

  <path class="d-line-strong" d="M288 167 V161" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line" d="M316 360 V150 H299" marker-end="url(#attention-additive-arrow)"/>

  <circle class="d-node" cx="288" cy="150" r="9"/>

  <text class="d-label" x="288" y="154" text-anchor="middle">×</text>

  <path class="d-line-accent" d="M288 141 L295.7 111.4" marker-end="url(#attention-additive-arrow-accent)" opacity="1.00"/>

  <path class="d-line" d="M366 341 V324" marker-end="url(#attention-additive-arrow)"/>

  <path class="d-line-strong" d="M378 360 V324" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-feedforward" x="360" y="298" width="36" height="24" rx="4"/>

  <text class="d-math" x="378" y="313" text-anchor="middle">e<tspan class="d-note" dy="3">i4</tspan><tspan dy="-3"> </tspan></text>

  <path class="d-line-strong" d="M378 298 V276" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line-strong" d="M378 252 V240"/>

  <rect class="d-box d-role-attention" x="370" y="214.7" width="16" height="25.3" rx="1"/>

  <text class="d-note" x="378" y="208.7" text-anchor="middle">0.23</text>

  <path class="d-line-strong" d="M378 196.7 V161" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line" d="M406 360 V150 H389" marker-end="url(#attention-additive-arrow)"/>

  <circle class="d-node" cx="378" cy="150" r="9"/>

  <text class="d-label" x="378" y="154" text-anchor="middle">×</text>

  <path class="d-line-accent" d="M378 141 L314.6 103.6" marker-end="url(#attention-additive-arrow-accent)" opacity="0.57"/>

  <path class="d-line" d="M456 341 V324" marker-end="url(#attention-additive-arrow)"/>

  <path class="d-line-strong" d="M468 360 V324" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-feedforward" x="450" y="298" width="36" height="24" rx="4"/>

  <text class="d-math" x="468" y="313" text-anchor="middle">e<tspan class="d-note" dy="3">i5</tspan><tspan dy="-3"> </tspan></text>

  <path class="d-line-strong" d="M468 298 V276" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line-strong" d="M468 252 V240"/>

  <rect class="d-box d-role-attention" x="460" y="231.2" width="16" height="8.8" rx="1"/>

  <text class="d-note" x="468" y="225.2" text-anchor="middle">0.08</text>

  <path class="d-line-strong" d="M468 213.2 V161" marker-end="url(#attention-additive-arrow-strong)"/>

  <path class="d-line" d="M496 360 V150 H479" marker-end="url(#attention-additive-arrow)"/>

  <circle class="d-node" cx="468" cy="150" r="9"/>

  <text class="d-label" x="468" y="154" text-anchor="middle">×</text>

  <path class="d-line-accent" d="M468 141 L316.4 99.5" marker-end="url(#attention-additive-arrow-accent)" opacity="0.33"/>

  <text class="d-math" x="545" y="314" text-anchor="start">e<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3"> = v</tspan><tspan class="d-note" dy="3">a</tspan><tspan dy="-3">ᵀ tanh(W</tspan><tspan class="d-note" dy="3">a</tspan><tspan dy="-3"> s</tspan><tspan class="d-note" dy="3">i−1</tspan><tspan dy="-3"> + U</tspan><tspan class="d-note" dy="3">a</tspan><tspan dy="-3"> h</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3">)</tspan></text>

  <text class="d-math" x="545" y="267" text-anchor="start">α<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3"> = softmax</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3">(e</tspan><tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">),  Σ</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3"> α</tspan><tspan class="d-note" dy="3">ij</tspan><tspan dy="-3"> = 1</tspan></text>

  <text class="d-note" x="545" y="205" text-anchor="start">bar height and line strength = α<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3"> </tspan></text>

  <circle class="d-node" cx="300" cy="95" r="14"/>

  <text class="d-label" x="300" y="99" text-anchor="middle">Σ</text>

  <text class="d-math" x="300" y="69" text-anchor="middle">c<tspan class="d-note" dy="3">i</tspan><tspan dy="-3"> = Σ</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3"> α</tspan><tspan class="d-note" dy="3">ij</tspan><tspan dy="-3"> h</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3"> </tspan></text>

  <path class="d-line-accent" d="M314 95 H730" marker-end="url(#attention-additive-arrow-accent)"/>

  <text class="d-math" x="520" y="86" text-anchor="middle">c<tspan class="d-note" dy="3">i</tspan><tspan dy="-3"> </tspan></text>

  <rect class="d-box d-role-output" x="732" y="80" width="96" height="30" rx="5"/>

  <text class="d-math" x="780" y="98" text-anchor="middle">s<tspan class="d-note" dy="3">i</tspan><tspan dy="-3"> </tspan></text>

  <path class="d-line-strong" d="M780 326 V112" marker-end="url(#attention-additive-arrow-strong)"/>

  <text class="d-math" x="768" y="160" text-anchor="end">s<tspan class="d-note" dy="3">i</tspan><tspan dy="-3"> = f(s</tspan><tspan class="d-note" dy="3">i−1</tspan><tspan dy="-3">, y</tspan><tspan class="d-note" dy="3">i−1</tspan><tspan dy="-3">, c</tspan><tspan class="d-note" dy="3">i</tspan><tspan dy="-3">)</tspan></text>

  <path class="d-line-strong" d="M866 95 H830" marker-end="url(#attention-additive-arrow-strong)"/>

  <text class="d-math" x="854" y="85" text-anchor="middle">y<tspan class="d-note" dy="3">i−1</tspan><tspan dy="-3"> </tspan></text>

  <path class="d-line-strong" d="M780 80 V53" marker-end="url(#attention-additive-arrow-strong)"/>

  <rect class="d-box d-role-output" x="758" y="29" width="44" height="22" rx="4"/>

  <text class="d-math" x="780" y="43" text-anchor="middle">y<tspan class="d-note" dy="3">i</tspan><tspan dy="-3"> </tspan></text>

  <text class="d-math" x="746" y="44" text-anchor="end">p(y<tspan class="d-note" dy="3">i</tspan><tspan dy="-3"> | …) = g(y</tspan><tspan class="d-note" dy="3">i−1</tspan><tspan dy="-3">, s</tspan><tspan class="d-note" dy="3">i</tspan><tspan dy="-3">, c</tspan><tspan class="d-note" dy="3">i</tspan><tspan dy="-3">)</tspan></text>

  <text class="d-note" x="40" y="488" text-anchor="start">Bahdanau, Cho &amp; Bengio 2014 (RNNsearch): gated hidden units, 1000 per direction in the encoder and 1000 in the decoder;</text>

  <text class="d-note" x="40" y="503" text-anchor="start">the score a(s<tspan class="d-note" dy="3">i−1</tspan><tspan dy="-3">, h</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3">) is a one-hidden-layer network trained jointly with the translator. The α values shown are illustrative.</tspan></text>

  </svg>

  '
diagram_caption: For one decoder step, the previous state s_(i−1) is scored against every bidirectional annotation h_j; a softmax turns the scores into weights α_ij, and their weighted sum c_i feeds the new state s_i, which emits y_i.
notable_models:
- name: RNNsearch (Bahdanau, Cho and Bengio)
  year: 2014
  note: Soft-searches the source for each target word; the authors report English-to-French translation comparable to the existing phrase-based system.
- name: Luong attention
  year: 2015
  note: Compared a global approach that attends to all source words with a local one that looks at a subset; local attention gained 5.0 BLEU over non-attentional systems that already used dropout, and the authors report a new state of the art of 25.9 BLEU on WMT 2015 English to German with an ensemble.
- name: Pointer Networks
  year: 2015
  note: Use attention as a pointer that selects a member of the input sequence as the output, with the same additive score form, vᵀ tanh(W₁eⱼ + W₂dᵢ).
- name: Google Neural Machine Translation (GNMT)
  year: 2016
  note: Its attention function is a feed-forward network with one hidden layer of 1,024 nodes, connecting the bottom decoder layer to the top encoder layer.
- name: Transformer
  year: 2017
  note: Based solely on attention mechanisms, dispensing with recurrence and convolutions; it uses scaled dot-product attention rather than additive attention.
resources:
- kind: paper
  title: Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho and Bengio, 2014)
  url: https://arxiv.org/abs/1409.0473
- kind: blog
  title: Attention and Augmented Recurrent Neural Networks (Distill)
  url: https://distill.pub/2016/augmented-rnns/
- kind: blog
  title: Attention? Attention! (Lilian Weng)
  url: https://lilianweng.github.io/posts/2018-06-24-attention/
- kind: blog
  title: Visualizing A Neural Machine Translation Model (Jay Alammar)
  url: https://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/
learn_first:
- label: Seq2seq & the bottleneck it hit
  id: architecture-era/seq2seq
  tag: Architecture
- label: Softmax turns scores into weights
  tag: Math
- label: Recurrent networks (RNN/LSTM)
  tag: Architecture
tags:
- attention
- alignment
- translation
- classical-dl
- foundational
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/1409.0473
  title: Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho and Bengio, 2014)
  trust: 0.95
- url: https://arxiv.org/abs/1508.04025
  title: Effective Approaches to Attention-based Neural Machine Translation (Luong, Pham and Manning, 2015)
  trust: 0.95
- url: https://arxiv.org/abs/1506.03134
  title: Pointer Networks (Vinyals, Fortunato and Jaitly, 2015)
  trust: 0.95
- url: https://arxiv.org/abs/1609.08144
  title: 'Google''s Neural Machine Translation System: Bridging the Gap between Human and Machine Translation (Wu et al., 2016)'
  trust: 0.95
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.95
---

## What Changed

The authors trained both models on the same data in the same way, so the table above isolates
the change: the decoder may **search the source** instead of reading one vector.

How to read it (it follows Sections 3 to 5 and Table 1 of the paper):

- **RNNencdec** is the encoder–decoder of Cho et al.; **RNNsearch** is the new model. The paper
  notes that RNNsearch turns back into the encoder–decoder if every cᵢ is fixed to the forward
  encoder's last state.
- **BLEU** scores how closely a translation matches human reference translations; higher is better.
  The test set is WMT 2014 news-test-2014, **3,003** sentences.
- **"No unknown words"** keeps only test sentences whose words, and whose reference's words, are all
  in the 30,000-word vocabulary. The gap between the two BLEU rows shows how much unknown words cost.
- **The cost row** is the price of the search: one score for every pair of a source word and an
  output word.

## Key Ideas

- **A vector per source word:** a bidirectional encoder gives every word an annotation that
  summarises the words on both sides of it.
- **Scores from a small network:** a feed-forward layer compares the decoder's previous state with
  each annotation; Vaswani et al. call this form additive attention.
- **A fresh context for every output word:** a softmax over the scores gives weights, and the
  weighted sum of annotations is that word's context vector.
- **A by-product you can plot:** the weights form a soft word alignment between the two sentences.

## How It Works

The figure above shows **one decoder step i**. Follow it from the bottom: source words, encoder
annotations, scores, weights, context, new decoder state, output word.

A **vector** is a list of numbers. An **RNN** (recurrent neural network) reads a sequence one item
per step and carries a **hidden state** from step to step; the paper uses a **gated hidden unit**,
an alternative to the LSTM.

1. **Source words in (x₁ … x₅).** Each word comes from a vocabulary of the **30,000** most frequent
   words in its language, and becomes a learned vector of **620** numbers. Other words become
   **[UNK]**.
2. **Bidirectional encoder (the boxes →h and ←h).** A forward RNN reads the sentence left to right,
   a backward RNN right to left, **1,000** hidden units each. Word j's **annotation** hⱼ joins the
   two states (**2,000** numbers), so it summarises the words before and after word j, with a focus
   on the words around it.
3. **Score every annotation (the boxes e).** The decoder's **previous** state sᵢ₋₁ is compared with
   every hⱼ by the alignment model, eᵢⱼ = v_aᵀ tanh(W_a sᵢ₋₁ + U_a hⱼ), a network with one hidden
   layer of **1,000** units. U_a hⱼ does not depend on i, so the paper computes it once per sentence.
4. **Softmax into weights (the bars α).** αᵢⱼ = exp(eᵢⱼ) / Σₖ exp(eᵢₖ): every weight is positive and
   the weights of step i add up to 1. The paper reads αᵢⱼ as the probability that output word i is
   aligned to, or translated from, source word j.
5. **Weighted sum (the × and Σ circles).** The context vector cᵢ = Σⱼ αᵢⱼ hⱼ: each annotation is
   multiplied by its weight and the results are added. The stronger lines carry the larger weights.
6. **New decoder state (sᵢ).** sᵢ = f(sᵢ₋₁, yᵢ₋₁, cᵢ): the gated unit, **1,000** hidden units, takes
   the previous state, the previous output word and the new context.
7. **Output word (yᵢ).** The probability of each target word, g(yᵢ₋₁, sᵢ, cᵢ), comes from a hidden
   layer of **500** maxout units followed by a softmax over the 30,000-word target vocabulary.
8. **Next step.** For word i + 1 the scores are computed again from the new state sᵢ, so the weights
   move to other source words. Over a whole sentence the alignment model runs **source length ×
   target length** times.

## Worked Example

**One decoder step, by hand.** The source is "the black cat"; the decoder has written "le" and is
about to write the next French word. All numbers are an example, not from a trained model; vectors
have **2** numbers instead of the paper's 1,000 and 2,000, so every step can be checked.

**The inputs:**

| Word | Annotation hⱼ |
| --- | --- |
| the | (−1, 0) |
| black | (1, −1) |
| cat | (1, 0) |

- The decoder's previous state is $s_{i-1} = (0, 1)$.
- To keep the arithmetic short, $W_a$ and $U_a$ are the identity matrix (they leave a vector as it
  is), and $v_a = (1, 1)$. A trained model learns all three.
- The only tanh values needed: $\tanh(1) \approx 0.762$, $\tanh(0) = 0$, $\tanh(-1) \approx -0.762$.

**Step 1: score each source word,** $e_{ij} = v_a^\top \tanh(W_a s_{i-1} + U_a h_j)$, with
$W_a s_{i-1} = (0, 1)$:

- **the:** (0, 1) + (−1, 0) = (−1, 1); tanh gives (−0.762, 0.762); 1 × (−0.762) + 1 × 0.762 = **0**.
- **black:** (0, 1) + (1, −1) = (1, 0); tanh gives (0.762, 0); 0.762 + 0 = **0.762**.
- **cat:** (0, 1) + (1, 0) = (1, 1); tanh gives (0.762, 0.762); 0.762 + 0.762 = **1.524**.

**Step 2: softmax into weights** (what the figure draws):

1. $e^{0} = 1$, $e^{0.762} \approx 2.143$ and $e^{1.524} \approx 4.591$; the total is 7.733.
2. Weights: the 1 / 7.733 ≈ **0.129**, black 2.143 / 7.733 ≈ **0.277**, cat 4.591 / 7.733 ≈
   **0.594**. They add up to 1.

**Step 3: the context vector,** $c_i = \sum_j \alpha_{ij} h_j$:

- 0.129 × (−1, 0) = (−0.129, 0)
- 0.277 × (1, −1) = (0.277, −0.277)
- 0.594 × (1, 0) = (0.594, 0)
- Sum: **(0.742, −0.277)**, the context for this step.

**For comparison:** an equal blend of the three annotations would be (1 / 3, −1 / 3) ≈ (0.333,
−0.333). The weighted context leans towards "cat", whose annotation is (1, 0).

**What this means:** before writing the word after "le", the decoder drew most (**0.594**) on "cat",
the word that French puts next ("le chat noir"). At the next step the decoder state changes, all
three scores are computed again, and the weights can move to "black". In the encoder–decoder
without attention, every step would have read the same single vector.
