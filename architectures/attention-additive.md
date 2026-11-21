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
description: "Bahdanau attention: the decoder soft-searches all encoder states instead of one fixed-length vector."
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
  motivation: "The 2014 sequence-to-sequence (seq2seq) recipe encoded an entire source sentence into a single fixed-length vector, then decoded the translation from it. A 40-word sentence and a 4-word sentence get the same amount of room; Bahdanau, Cho and Bengio report that the basic encoder–decoder deteriorates rapidly as input length increases."
  approach: "Bahdanau, Cho and Bengio conjectured that the fixed-length vector is a bottleneck. The encoder already produces a vector for every source position, so they keep all of them and let the decoder decide, at each step, which ones to read."
  solution: "At every decoding step a small feed-forward network scores how well the decoder's current state aligns with each encoder state. A softmax turns those scores into weights that sum to one, and the weighted average of the encoder states becomes a context vector — recomputed fresh for every output token. Because the scoring network is differentiable, the alignment is learned jointly with translation, not hand-designed."
  outcome: "The authors report that RNNsearch-50 shows no performance deterioration even on sentences of 50 or more words, and that its English-to-French translation is comparable to the existing phrase-based system. Plotted, the learned weights form a soft word alignment: the model translates 'European Economic Area' into 'zone économique européenne', correctly reversing the word order. The Transformer (2017) drops recurrence and relies on attention alone."

build_steps:
  - { title: "1. Encode every source word", detail: "Run the source sentence through an encoder (originally a bidirectional RNN) to get one hidden state per word — a bank of vectors, not a single summary." }
  - { title: "2. Score alignment for each word", detail: "At decoding step t, take the decoder's previous state and compare it against each encoder state with a small feed-forward net, producing one raw relevance score (energy) per source word." }
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
  - "Every source word stays directly reachable; the authors report no deterioration on sentences of 50 or more words (RNNsearch-50)."
  - "The attention weights are interpretable: plotting them yields a soft word-alignment map as a free by-product."
  - "Fully differentiable, so alignment is learned end-to-end with translation rather than engineered."
  - "Context is dynamic — each output word gets its own focus over the source, matching how translation actually reorders words."

weaknesses:
  - "Still built on recurrence, so training remains sequential and cannot fully parallelize across time."
  - "The additive scoring net is computed for every (output, source) pair, adding cost that grows with sentence length."
  - "Attention here connects decoder to encoder only — the encoder still can't attend within itself, a limit the transformer later removes."

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 880 515" role="img" aria-label="Additive attention for one decoder step: a bidirectional encoder gives one annotation h_j per source word; the previous decoder state s_(i-1) is scored against every h_j by a small network, a softmax turns the scores into weights alpha_ij, and the weighted sum c_i feeds the new decoder state s_i, which emits y_i.">
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
  <text class="d-note" x="40" y="488" text-anchor="start">Bahdanau, Cho &amp; Bengio 2014 (RNNsearch): gated recurrent units, 1000 per direction in the encoder and 1000 in the decoder;</text>
  <text class="d-note" x="40" y="503" text-anchor="start">the score a(s<tspan class="d-note" dy="3">i−1</tspan><tspan dy="-3">, h</tspan><tspan class="d-note" dy="3">j</tspan><tspan dy="-3">) is a one-hidden-layer network trained jointly with the translator. The α values shown are illustrative.</tspan></text>
  </svg>
diagram_caption: "For one decoder step, the previous state s_(i−1) is scored against every bidirectional annotation h_j; a softmax turns the scores into weights α_ij, and their weighted sum c_i feeds the new state s_i, which emits y_i."
notable_models:
  - { name: "Bahdanau NMT (RNNsearch)", year: 2015, note: "Soft-searches the source for each target word; the authors report translation comparable to the phrase-based state of the art on English-to-French." }
  - { name: "Luong attention", year: 2015, note: "Compared a global approach that attends to all source words with a local one that looks at a subset; local attention gained 5.0 BLEU over non-attentional systems." }
  - { name: "Pointer Networks", year: 2015, note: "Use attention as a pointer that selects a member of the input sequence as the output." }
  - { name: "Transformer", year: 2017, note: "Based solely on attention mechanisms, dispensing with recurrence and convolutions." }

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
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/1409.0473", title: "Neural Machine Translation by Jointly Learning to Align and Translate (Bahdanau, Cho & Bengio, 2015)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1508.04025", title: "Effective Approaches to Attention-based Neural Machine Translation (Luong, Pham & Manning, arXiv:1508.04025)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1506.03134", title: "Pointer Networks (Vinyals, Fortunato & Jaitly, arXiv:1506.03134)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., arXiv:1706.03762)", trust: 0.95 }
  - { url: "https://lilianweng.github.io/posts/2018-06-24-attention/", title: "Attention? Attention! (Lilian Weng)", trust: 0.9 }
  - { url: "https://distill.pub/2016/augmented-rnns/", title: "Attention and Augmented Recurrent Neural Networks (Distill)", trust: 0.9 }
---

## What Changed
Seq2seq compressed an entire source sentence into one fixed-length vector, which Bahdanau, Cho and
Bengio conjectured is a bottleneck. They let the decoder *look back* at all of the encoder's
per-position states at every generation step.

A small feed-forward network scores the decoder's previous state against each encoder state, a softmax
turns those scores into weights that sum to one, and the weighted sum of encoder states becomes a
**context vector recomputed for each output token**.

The scoring network is trained jointly with the translator, and the weights form a soft word alignment.
"Attention Is All You Need" (2017) later dropped recurrence and built a model on attention alone.

## Key Ideas
- **Soft alignment** — instead of one summary vector, the decoder attends over every encoder state,
  so information from any source position is directly reachable.
- **Additive scoring** — relevance is scored by a small feed-forward net, $v_a^\top \tanh(W_a s_{t-1} + U_a h_j)$,
  then normalised by softmax into attention weights.
- **Dynamic context** — the context vector is re-derived at each decoding step, so the model focuses on
  different source words as it generates.
- **Interpretability** — the learned attention weights form a soft alignment map, showing which source
  tokens drive each output token.
