---
id: architecture-era/transformer
type: architecture-era
slug: transformer
schema_version: 2.5
title: "The Transformer"
name: "Transformer"
era: scaling
era_range: "2017–present"
year: 2017
family: llm
core_innovation: "Replace recurrence and convolution with attention, so every position can attend to every other position and computation within a sequence runs in parallel."
description: "A neural network for sequences, such as the words of a sentence, in which every word gathers information from every other word through attention, with no recurrence and no convolution."
key_features: ["Self-attention", "Multi-head attention", "Positional encoding", "Position-wise feed-forward sub-layer", "Parallel computation across positions"]
key_models: ["Transformer (2017)", "BERT (2018)", "GPT (2018)", "T5 (2019)"]
paper_title: "Attention Is All You Need (Vaswani et al., 2017)"
paper_url: "https://arxiv.org/abs/1706.03762"
influenced_by: [architecture-era/seq2seq, architecture-era/attention-additive]
led_to: [architecture-era/bert, architecture-era/gpt, architecture-era/vision-transformer]

analogy:
  title: "A room where everyone reads everyone's notes at once"
  body: "This is an analogy, not the mechanism. Picture every word of a sentence as a person holding a note. Each person writes a question (the query), a label on their note (the key) and the note itself (the value). Everyone compares their question with every label at the same moment, then copies a blend of the notes, taking most from the best-matching labels. That simultaneous comparison is self-attention; the exact rule is the key equation below."

narrative:
  motivation: "**Recurrent networks compute one position at a time.** The paper is by **Vaswani et al. (2017)**.\n\n- **State of the art then:** the authors write that recurrent networks had been firmly established as the state of the art in sequence modelling and translation.\n- **The limit:** a recurrent network computes **one position after another**, and the authors note that this sequential nature **precludes parallelization** within training examples."
  approach: "**Use attention only.** The paper proposes a model based solely on attention mechanisms, dispensing with **recurrence and convolutions** entirely.\n\n- **Direct connections:** every position attends to every other position directly.\n- **Fewer sequential steps:** a self-attention layer needs a **constant number** of sequential operations; a recurrent layer needs a number that **grows with sequence length** (the paper's Table 1)."
  solution: "**Two sub-layers in every layer**, stacked six times.\n\n- **Sub-layers:** multi-head **self-attention**, then a **feed-forward network** applied to every position.\n- **Wrapping:** each sub-layer is wrapped as `LayerNorm(x + Sublayer(x))`.\n- **Depth:** the encoder and the decoder each stack **six** such layers.\n- **Word order:** the model contains no recurrence and no convolution, so **sine and cosine positional encodings** are added to the input embeddings to give it the order of the tokens."
  outcome: "**Translation results the authors report on WMT 2014**, in BLEU (a score of how closely a translation matches reference translations):\n\n- **English to German:** **28.4 BLEU**.\n- **English to French:** **41.8 BLEU**, after **3.5 days** of training on **eight GPUs**.\n- **Base model:** trained in about **12 hours**.\n\n**What was built from it:**\n\n- **BERT:** built from its encoder.\n- **GPT:** a decoder-only stack.\n- **Vision Transformer:** applies it to image patches."

key_equation:
  latex: "\\mathrm{Attention}(Q,K,V)=\\mathrm{softmax}\\!\\left(\\frac{QK^{\\top}}{\\sqrt{d_k}}\\right)V"
  plain: "In words: compare each token's query with every key (the dot products in QKᵀ), divide those scores by √d_k, turn each token's row of scores into weights that add up to 1 with softmax, and use the weights to average the values V. The worked example above does exactly this by hand for two tokens."
  terms:
    - { symbol: "Q", meaning: "the queries: one row per token, what that token is looking for" }
    - { symbol: "K", meaning: "the keys: one row per token, what each token is matched on" }
    - { symbol: "V", meaning: "the values: one row per token, the content a token passes on when it is attended to" }
    - { symbol: "d_k", meaning: "the length of each query and key; 64 in the paper's base model (512 / 8 heads)" }
    - { symbol: "softmax", meaning: "turns a row of scores into positive weights that add up to 1: e to the power of each score, divided by the sum of those terms" }

strengths:
  - "**Short paths between words:** a self-attention layer connects any two positions with a **constant number** of sequential operations, against **O(n)** for a recurrent layer (Table 1 of the paper)."
  - "**Training time:** the authors report training the base model in about **12 hours** and the big model in **3.5 days** on **eight NVIDIA P100 GPUs**."
  - "**Scaling:** **Kaplan et al. (2020)** report that language-model loss scales as a **power law** with model size, dataset size and the compute used for training."
  - "**Beyond text:** the **Vision Transformer** applies a pure Transformer directly to sequences of image patches."

weaknesses:
  - "**Cost grows with length squared:** self-attention costs **O(n²·d)** per layer, which grows with the square of the sequence length n; the paper notes it is faster than a recurrent layer when n is smaller than the vector width d."
  - "**No sense of order:** the model has no built-in notion of order, so **position information must be added** to the inputs."
  - "**Trails on mid-sized image datasets:** the Vision Transformer authors report accuracies **a few percentage points below ResNets** of comparable size when trained on mid-sized datasets such as ImageNet without strong regularization."

visual:
  kind: distribution
  tokens: ["cats", "sleep"]
  logits: [2, 3]
  temperatures: [1, 1.4142]
  panel_titles: ["Raw scores 2 and 3", "Scores divided by √2"]
  caption: "The attention weights of the token \"sleep\" over the two tokens of the worked example, computed from its scores 2 and 3. Left: softmax of the raw scores. Right: the scores divided by √d_k = √2 ≈ 1.414 first, as the Transformer does. The numbers are an example, not from a trained model."

comparison:
  before: "Recurrent layer"
  after: "Self-attention layer"
  rows:
    - { aspect: "Can the positions of one training example be computed at the same time?", before: "No: the authors note the sequential nature precludes parallelization within training examples", after: "Yes: a constant number of sequential operations per layer", source: "https://arxiv.org/abs/1706.03762" }
    - { aspect: "Steps that must run one after another (n tokens)", before: "O(n)", after: "O(1)", source: "https://arxiv.org/abs/1706.03762" }
    - { aspect: "Longest path between two positions", before: "O(n)", after: "O(1)", source: "https://arxiv.org/abs/1706.03762" }
    - { aspect: "Work per layer (n tokens, vector width d)", before: "O(n · d²)", after: "O(n² · d)", source: "https://arxiv.org/abs/1706.03762" }

key_numbers:
  columns: ["Setting", "Base model", "Big model"]
  rows:
    - { cells: ["Layers in the encoder, and again in the decoder (N)", "6", "6"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["Numbers in each token's vector (d_model)", "512", "1024"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["Inner width of the feed-forward network (d_ff)", "2048", "4096"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["Attention heads (h)", "8", "16"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["Length of each head's queries, keys and values (d_k = d_v)", "64", "64"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["Dropout rate (P_drop)", "0.1", "0.3"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["Training steps", "100,000", "300,000"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["Training time on 8 NVIDIA P100 GPUs", "about 12 hours (0.4 s per step)", "3.5 days (1.0 s per step)"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["Parameters", "65 million", "213 million"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["BLEU, WMT 2014 English to German", "27.3", "28.4"], source: "https://arxiv.org/abs/1706.03762" }
    - { cells: ["BLEU, WMT 2014 English to French", "38.1", "41.8"], source: "https://arxiv.org/abs/1706.03762" }

misunderstandings:
  - misunderstanding: "The Transformer is a decoder-only model like GPT."
    truth: "The original Transformer was built for translation and has **two stacks**: an encoder of six layers that reads the source sentence and a decoder of six layers that writes the translation while attending over the encoder's output. GPT (2018) later trained a **12-layer decoder-only** Transformer."
    source: "https://arxiv.org/abs/1706.03762"
  - misunderstanding: "Attention works out the order of the words by itself."
    truth: "Attention compares every token with every other token and ignores where they stand. Because the model contains no recurrence and no convolution, the authors **add positional encodings** (sine and cosine values) to the input embeddings. Learned position embeddings gave **nearly identical results** in their tests (Table 3, row E)."
    source: "https://arxiv.org/abs/1706.03762"
  - misunderstanding: "Self-attention is cheaper than a recurrent layer at every sequence length."
    truth: "Per layer, self-attention costs **O(n²·d)** and a recurrent layer **O(n·d²)**. The paper states that self-attention is faster when the sequence length n is **smaller than** the vector width d. For long sequences the n² term dominates."
    source: "https://arxiv.org/abs/1706.03762"
  - misunderstanding: "Dividing the scores by the square root of the key length is a cosmetic detail."
    truth: "The authors give a reason: if the entries of a query and a key are independent with mean 0 and variance 1, their dot product has **variance $d_k$**. For large $d_k$ the scores grow large in magnitude, which pushes softmax into regions where it has **extremely small gradients**. Dividing by $\\sqrt{d_k}$ counteracts this."
    source: "https://arxiv.org/abs/1706.03762"

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 562" role="img" aria-label="The original Transformer: on the left an encoder layer, multi-head self-attention then feed-forward, each followed by Add and Norm, repeated six times; on the right a decoder layer, masked self-attention, attention over the encoder output, and feed-forward, each followed by Add and Norm, repeated six times, then a linear layer and softmax. Embeddings plus positional encoding enter at the bottom and dashed lines are residual connections.">
  <defs><marker id="transformer-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="transformer-arrow-strong" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="transformer-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <text class="d-title" x="30" y="40" text-anchor="start">Base model (Vaswani et al. 2017)</text>
  <text class="d-note" x="30" y="60" text-anchor="start">N = 6 identical layers in each stack</text>
  <text class="d-note" x="30" y="76" text-anchor="start">d<tspan class="d-note" dy="3">model</tspan><tspan dy="-3"> = 512 · h = 8 heads, d</tspan><tspan class="d-note" dy="3">k</tspan><tspan dy="-3"> = d</tspan><tspan class="d-note" dy="3">v</tspan><tspan dy="-3"> = 64</tspan></text>
  <text class="d-note" x="30" y="92" text-anchor="start">d<tspan class="d-note" dy="3">ff</tspan><tspan dy="-3"> = 2048: feed-forward inner width</tspan></text>
  <text class="d-note" x="30" y="112" text-anchor="start">post-norm: LayerNorm(x + Sublayer(x))</text>
  <text class="d-note" x="30" y="128" text-anchor="start">dashed: residual connections</text>
  <text class="d-note" x="30" y="144" text-anchor="start">the final encoder output feeds the</text>
  <text class="d-note" x="30" y="158" text-anchor="start">encoder–decoder attention of every decoder layer</text>
  <rect class="d-frame" x="145" y="264" width="210" height="206" rx="8"/>
  <text class="d-title" x="135" y="360" text-anchor="end">Encoder</text>
  <text class="d-note" x="135" y="376" text-anchor="end">× 6 layers</text>
  <text class="d-label" x="260" y="552" text-anchor="middle">Inputs</text>
  <rect class="d-box d-role-embedding" x="180" y="512" width="160" height="24" rx="5"/>
  <text class="d-label" x="260" y="528" text-anchor="middle">Input embedding</text>
  <path class="d-line-strong" d="M260 512 V501" marker-end="url(#transformer-arrow-strong)"/>
  <circle class="d-node" cx="260" cy="490" r="9"/>
  <text class="d-label" x="260" y="494" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="96" y="482" width="60" height="20" rx="4"/>
  <text class="d-note" x="126" y="496" text-anchor="middle">pos. enc.</text>
  <path class="d-line-strong" d="M156 490 H249" marker-end="url(#transformer-arrow-strong)"/>
  <path class="d-line-strong" d="M260 481 V462 M220 462 H300"/>
  <path class="d-line-strong" d="M220 462 V454" marker-end="url(#transformer-arrow-strong)"/>
  <path class="d-line-strong" d="M260 462 V454" marker-end="url(#transformer-arrow-strong)"/>
  <path class="d-line-strong" d="M300 462 V454" marker-end="url(#transformer-arrow-strong)"/>
  <rect class="d-box d-role-attention" x="180" y="420" width="160" height="32" rx="5"/>
  <text class="d-label" x="260" y="434" text-anchor="middle">Multi-head attention</text>
  <text class="d-note" x="260" y="447" text-anchor="middle">self: Q, K, V from below</text>
  <path class="d-line-back" d="M260 474 H160 V390 H178" marker-end="url(#transformer-arrow)"/>
  <path class="d-line-strong" d="M260 420 V402" marker-end="url(#transformer-arrow-strong)"/>
  <rect class="d-box " x="180" y="380" width="160" height="20" rx="5"/>
  <text class="d-label" x="260" y="394" text-anchor="middle">Add &amp; Norm</text>
  <path class="d-line-strong" d="M260 380 V350" marker-end="url(#transformer-arrow-strong)"/>
  <path class="d-line-back" d="M260 364 H160 V286 H178" marker-end="url(#transformer-arrow)"/>
  <rect class="d-box d-role-feedforward" x="180" y="316" width="160" height="32" rx="5"/>
  <text class="d-label" x="260" y="330" text-anchor="middle">Feed-forward</text>
  <text class="d-note" x="260" y="343" text-anchor="middle">per position: 512 → 2048 → 512</text>
  <path class="d-line-strong" d="M260 316 V298" marker-end="url(#transformer-arrow-strong)"/>
  <rect class="d-box " x="180" y="276" width="160" height="20" rx="5"/>
  <text class="d-label" x="260" y="290" text-anchor="middle">Add &amp; Norm</text>
  <path class="d-line-accent" d="M260 276 V240 H470 V364 H600 V350" marker-end="url(#transformer-arrow-accent)"/>
  <path class="d-line-accent" d="M560 364 V350" marker-end="url(#transformer-arrow-accent)"/>
  <text class="d-label" x="365" y="232" text-anchor="middle">encoder output → K, V</text>
  <rect class="d-frame" x="505" y="172" width="210" height="298" rx="8"/>
  <text class="d-title" x="725" y="320" text-anchor="start">Decoder</text>
  <text class="d-note" x="725" y="336" text-anchor="start">× 6 layers</text>
  <text class="d-label" x="600" y="552" text-anchor="middle">Outputs (shifted right)</text>
  <rect class="d-box d-role-embedding" x="520" y="512" width="160" height="24" rx="5"/>
  <text class="d-label" x="600" y="528" text-anchor="middle">Output embedding</text>
  <path class="d-line-strong" d="M600 512 V501" marker-end="url(#transformer-arrow-strong)"/>
  <circle class="d-node" cx="600" cy="490" r="9"/>
  <text class="d-label" x="600" y="494" text-anchor="middle">+</text>
  <rect class="d-box d-role-position" x="712" y="482" width="60" height="20" rx="4"/>
  <text class="d-note" x="742" y="496" text-anchor="middle">pos. enc.</text>
  <path class="d-line-strong" d="M712 490 H611" marker-end="url(#transformer-arrow-strong)"/>
  <path class="d-line-strong" d="M600 481 V462 M560 462 H640"/>
  <path class="d-line-strong" d="M560 462 V454" marker-end="url(#transformer-arrow-strong)"/>
  <path class="d-line-strong" d="M600 462 V454" marker-end="url(#transformer-arrow-strong)"/>
  <path class="d-line-strong" d="M640 462 V454" marker-end="url(#transformer-arrow-strong)"/>
  <rect class="d-box d-role-attention" x="520" y="420" width="160" height="32" rx="5"/>
  <text class="d-label" x="600" y="434" text-anchor="middle">Masked multi-head attention</text>
  <text class="d-note" x="600" y="447" text-anchor="middle">position i sees only ≤ i</text>
  <path class="d-line-back" d="M600 474 H700 V390 H682" marker-end="url(#transformer-arrow)"/>
  <path class="d-line-strong" d="M600 420 V402" marker-end="url(#transformer-arrow-strong)"/>
  <rect class="d-box " x="520" y="380" width="160" height="20" rx="5"/>
  <text class="d-label" x="600" y="394" text-anchor="middle">Add &amp; Norm</text>
  <path class="d-line-strong" d="M640 380 V350" marker-end="url(#transformer-arrow-strong)"/>
  <path class="d-line-back" d="M640 372 H700 V286 H682" marker-end="url(#transformer-arrow)"/>
  <rect class="d-box d-role-attention" x="520" y="316" width="160" height="32" rx="5"/>
  <text class="d-label" x="600" y="330" text-anchor="middle">Multi-head attention</text>
  <text class="d-note" x="600" y="343" text-anchor="middle">Q: decoder · K, V: encoder</text>
  <path class="d-line-strong" d="M600 316 V298" marker-end="url(#transformer-arrow-strong)"/>
  <rect class="d-box " x="520" y="276" width="160" height="20" rx="5"/>
  <text class="d-label" x="600" y="290" text-anchor="middle">Add &amp; Norm</text>
  <path class="d-line-strong" d="M600 276 V258" marker-end="url(#transformer-arrow-strong)"/>
  <path class="d-line-back" d="M600 266 H700 V194 H682" marker-end="url(#transformer-arrow)"/>
  <rect class="d-box d-role-feedforward" x="520" y="224" width="160" height="32" rx="5"/>
  <text class="d-label" x="600" y="238" text-anchor="middle">Feed-forward</text>
  <text class="d-note" x="600" y="251" text-anchor="middle">per position: 512 → 2048 → 512</text>
  <path class="d-line-strong" d="M600 224 V206" marker-end="url(#transformer-arrow-strong)"/>
  <rect class="d-box " x="520" y="184" width="160" height="20" rx="5"/>
  <text class="d-label" x="600" y="198" text-anchor="middle">Add &amp; Norm</text>
  <path class="d-line-strong" d="M600 184 V156" marker-end="url(#transformer-arrow-strong)"/>
  <rect class="d-box d-role-output" x="520" y="132" width="160" height="22" rx="5"/>
  <text class="d-label" x="600" y="147" text-anchor="middle">Linear</text>
  <path class="d-line-strong" d="M600 132 V116" marker-end="url(#transformer-arrow-strong)"/>
  <rect class="d-box d-role-output" x="520" y="92" width="160" height="22" rx="5"/>
  <text class="d-label" x="600" y="107" text-anchor="middle">Softmax</text>
  <path class="d-line-strong" d="M600 92 V76" marker-end="url(#transformer-arrow-strong)"/>
  <text class="d-label" x="600" y="64" text-anchor="middle">Output probabilities (next token)</text>
  </svg>
diagram_caption: "The original Transformer: six encoder layers (self-attention, feed-forward) and six decoder layers (masked self-attention, attention over the encoder output, feed-forward), each sub-layer wrapped in a residual and LayerNorm."
underlying_model_note: "The first figure is the original Transformer, with an encoder stack and a decoder stack. The model drawn below keeps one stack of the same kind of block: attention, then a feed-forward layer, each inside a residual connection."
notable_models:
  - { name: "Transformer", year: 2017, note: "The original encoder-decoder, built for translation: 28.4 BLEU for English to German and 41.8 BLEU for English to French on WMT 2014." }
  - { name: "BERT", year: 2018, note: "Bidirectional Encoder Representations from Transformers; its authors report new state-of-the-art results on eleven natural language processing tasks." }
  - { name: "GPT", year: 2018, note: "A 12-layer decoder-only Transformer with masked self-attention (768-number vectors, 12 heads), pretrained generatively and then fine-tuned on each task." }
  - { name: "T5", year: 2019, note: "Converts every text-based language problem into a text-to-text format." }
  - { name: "Vision Transformer (ViT)", year: 2020, note: "A pure Transformer applied directly to sequences of image patches." }
resources:
  - { kind: blog, title: "The Illustrated Transformer (Jay Alammar)", url: "https://jalammar.github.io/illustrated-transformer/" }
  - { kind: video, title: "Let's build GPT from scratch (Andrej Karpathy)", url: "https://www.youtube.com/watch?v=kCc8FmEb1nY" }
  - { kind: tutorial, title: "The Annotated Transformer (Harvard NLP)", url: "https://nlp.seas.harvard.edu/annotated-transformer/" }
  - { kind: paper, title: "Attention Is All You Need (Vaswani et al., 2017)", url: "https://arxiv.org/abs/1706.03762" }

learn_first:
  - { label: "Attention mechanism (additive/Bahdanau)", href: "/models/architecture/attention-additive", tag: "Architecture" }
  - { label: "Seq2seq & the bottleneck it hit", href: "/models/architecture/seq2seq", tag: "Architecture" }
  - { label: "Softmax & dot products", href: "/learn/concepts/softmax", tag: "Math" }

tags: [transformer, attention, self-attention, foundational, llm]
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.98 }
  - { url: "https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf", title: "Improving Language Understanding by Generative Pre-Training (Radford et al., 2018)", trust: 0.95 }
  - { url: "https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf", title: "Language Models are Unsupervised Multitask Learners (Radford et al., 2019)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (Brown et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1810.04805", title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2010.11929", title: "An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale (Dosovitskiy et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2001.08361", title: "Scaling Laws for Neural Language Models (Kaplan et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1910.10683", title: "Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer (Raffel et al., 2019)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2302.13971", title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2403.08295", title: "Gemma: Open Models Based on Gemini Research and Technology (Gemma Team, 2024)", trust: 0.95 }
  - { url: "https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.3/blob/main/config.json", title: "mistralai/Mistral-7B-Instruct-v0.3 — config.json", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen2.5-7B-Instruct/blob/main/config.json", title: "Qwen/Qwen2.5-7B-Instruct — config.json", trust: 0.9 }
  - { url: "https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json", title: "deepseek-ai/DeepSeek-V3 — config.json", trust: 0.9 }
  - { url: "https://huggingface.co/microsoft/Phi-3-mini-4k-instruct/blob/main/config.json", title: "microsoft/Phi-3-mini-4k-instruct — config.json", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1907.11692", title: "RoBERTa: A Robustly Optimized BERT Pretraining Approach (Liu et al., 2019)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2006.03654", title: "DeBERTa: Decoding-enhanced BERT with Disentangled Attention (He et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2003.10555", title: "ELECTRA: Pre-training Text Encoders as Discriminators Rather Than Generators (Clark et al., 2020)", trust: 0.95 }
  - { url: "https://huggingface.co/BAAI/bge-large-en-v1.5/blob/main/config.json", title: "BAAI/bge-large-en-v1.5 — config.json", trust: 0.9 }
  - { url: "https://huggingface.co/intfloat/e5-large-v2/blob/main/config.json", title: "intfloat/e5-large-v2 — config.json", trust: 0.9 }
  - { url: "https://huggingface.co/thenlper/gte-large/blob/main/config.json", title: "thenlper/gte-large — config.json", trust: 0.9 }
  - { url: "https://huggingface.co/google-t5/t5-base/blob/main/config.json", title: "google-t5/t5-base — config.json", trust: 0.9 }
  - { url: "https://huggingface.co/google/flan-t5-base/blob/main/config.json", title: "google/flan-t5-base — config.json", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1910.13461", title: "BART: Denoising Sequence-to-Sequence Pre-training for Natural Language Generation, Translation, and Comprehension (Lewis et al., 2019)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2001.08210", title: "Multilingual Denoising Pre-training for Neural Machine Translation (Liu et al., 2020)", trust: 0.95 }
  - { url: "https://huggingface.co/facebook/nllb-200-distilled-600M/blob/main/config.json", title: "facebook/nllb-200-distilled-600M — config.json", trust: 0.9 }
  - { url: "https://huggingface.co/google/ul2/blob/main/config.json", title: "google/ul2 — config.json", trust: 0.9 }
variants:
- name: Decoder-only (Autoregressive)
  subtitle: Causal attention · next-token prediction
  models:
  - GPT-2/3
  - Llama
  - Mistral/Mixtral
  - Qwen
  - DeepSeek
  - Phi
  - Gemma
- name: Encoder-only (Understanding)
  subtitle: Bidirectional · masked LM · embeddings
  models:
  - BERT
  - RoBERTa
  - DeBERTa
  - ELECTRA
  - BGE
  - E5
  - GTE
- name: Encoder-Decoder (Seq2Seq)
  subtitle: Bi-encoder + causal decoder · translation
  models:
  - T5
  - Flan-T5
  - BART
  - mBART
  - NLLB
  - UL2
---

## What Changed

Before the Transformer, the authors write, **recurrent networks** were firmly established as the
state of the art for sequence modelling and translation. A recurrent network reads a sentence one
position after another and carries a running summary forward, so step 50 waits for step 49.

How to read the table above (it restates the paper's Table 1):

- **n** is the number of tokens in the sequence; **d** is the number of values in each token's
  vector (512 in the base model).
- **O(...)**, read "order of", says how a cost grows: O(n) grows in step with n, O(1) stays the
  same however long the sequence is.
- **Steps one after another:** a recurrent layer needs n of them; self-attention needs a fixed
  number, so all positions of a sentence are computed at the same time.
- **Longest path:** how many steps information takes to travel between two positions. The
  authors argue that the shorter these paths, the easier it is to learn long-range dependencies.
- **The price:** self-attention compares every pair of positions, so its work grows with **n²**.

## Key Ideas

- **Self-attention is a soft lookup.** Each token issues a query, matches it against every key,
  and retrieves a weighted blend of the values.
- **Several heads run side by side.** The authors write that multi-head attention lets the model
  jointly attend to information from different representation subspaces at different positions.
- **Position is added, not assumed.** Positional encodings are added to the input embeddings.
- **Attention plus feed-forward, stacked.** Each layer pairs attention with a position-wise
  feed-forward network, both wrapped in a residual connection and layer normalization.

## How It Works

The figure above has **two stacks**. The encoder (left) reads the whole input sentence; the
decoder (right) produces the output sentence. Follow the arrows from the bottom.

A **token** is a piece of text, a word or part of a word. A **vector** is a list of numbers; in
the base model every token is carried as a vector of **512** numbers (called $d_{model}$).

1. **Input embedding.** A learned embedding turns each input token into a vector of 512
   numbers. The paper multiplies these embedding weights by $\sqrt{512}$.
2. **Positional encoding (the "+" circle).** Sine and cosine values that depend on the token's
   position are added to its vector. Without this step the model could not tell word order,
   because it has no recurrence and no convolution.
3. **Multi-head attention (encoder).** Every token computes a **query**, a **key** and a
   **value** from its vector with learned weights. Each query is scored against every key, the
   scores become weights, and the token receives a weighted blend of all values (the worked
   example below does this with numbers). The base model runs **8 heads** side by side, each with
   queries and keys of length 512 / 8 = **64**, and joins their outputs.
4. **Add & Norm.** The sub-layer's output is added to its input (the dashed **residual
   connection**) and the sum is normalised: LayerNorm(x + Sublayer(x)).
5. **Feed-forward.** The same small two-layer network is applied to every position on its own:
   512 numbers in, 2048 in the middle with a ReLU (negative values set to 0), 512 out. Then
   another Add & Norm.
6. **Repeat six times.** Steps 3 to 5 form one encoder layer; the encoder stacks **N = 6** of
   them. Its final output, one vector per input token, is passed to every decoder layer.
7. **Masked multi-head attention (decoder).** The decoder's input is the output sentence,
   shifted one position to the right (as the figure labels it). A **mask** sets the scores of later positions to minus infinity before softmax, so each
   position attends only to itself and earlier positions and cannot see the future.
8. **Encoder-decoder attention.** In the decoder's middle attention the **queries** come from
   the decoder and the **keys and values** from the encoder's output, so each output position
   can look at every word of the input sentence. A feed-forward network follows; the decoder
   also stacks **6** layers.
9. **Linear and softmax.** A linear layer turns the final vector into one score per vocabulary
   entry (for English to German, a vocabulary of about 37,000 tokens shared by both languages), and softmax turns the scores
   into the probabilities of the next token. The paper shares one weight matrix between the two
   embedding layers and this linear layer.

## Worked Example

**Scaled dot-product attention by hand**, for two tokens with queries and keys of length
$d_k = 2$. The numbers are made up and small so every step can be checked; a real model learns its
queries, keys and values and, in the base model, uses $d_k = 64$.

Each token has already been turned into a query, a key and a value:

| Token | Query | Key | Value |
| --- | --- | --- | --- |
| cats | (1, 0) | (2, 0) | (1, 3) |
| sleep | (1, 2) | (1, 1) | (5, 1) |

Follow the token **"sleep"**, whose query is (1, 2):

1. **Score it against every key** with a dot product (multiply matching entries, then add):
   - with "cats": 1 × 2 + 2 × 0 = **2**
   - with "sleep": 1 × 1 + 2 × 1 = **3**
2. **Scale:** divide by $\sqrt{d_k} = \sqrt{2}$ ≈ 1.414. The scores become 2 / 1.414 ≈ **1.414** and
   3 / 1.414 ≈ **2.121**.
3. **Softmax:** raise e to each score and divide by the total.
   - $e^{1.414} \approx 4.113$ and $e^{2.121} \approx 8.342$; the total is 12.455.
   - Weights: 4.113 / 12.455 ≈ **0.330** for "cats" and 8.342 / 12.455 ≈ **0.670** for
     "sleep". They add up to 1.
4. **Blend the values** with those weights:
   - 0.330 × (1, 3) = (0.33, 0.99)
   - 0.670 × (5, 1) = (3.35, 0.67)
   - Sum: **(3.68, 1.66)**, the new vector for "sleep".

The same steps for **"cats"** (query (1, 0)): scores 2 and 1, scaled 1.414 and 0.707, weights
0.670 and 0.330, new vector 0.670 × (1, 3) + 0.330 × (5, 1) = (0.67, 2.01) + (1.65, 0.33) =
**(2.32, 2.34)**. Written with matrices, both rows are computed at once: QKᵀ = [[2, 1], [2, 3]].

**What the figure shows:** the weights of "sleep" without and with the division by $\sqrt{d_k}$.
Without it, softmax(2, 3) gives 0.269 and 0.731; with it, 0.330 and 0.670. Dividing by $\sqrt{d_k}$
evens the weights out. The authors explain why it matters at full size: if the entries of a
query and a key are independent with mean 0 and variance 1, their dot product has variance $d_k$,
so at $d_k = 64$ raw scores spread far apart.

**What this means:** every output is a blend of all the value vectors, weighted by how well the
token's query matches each key. Here "sleep" takes about two thirds of its new vector from its
own value and one third from "cats". In the base model this runs in 8 heads at once, in every
attention block of the figure.
