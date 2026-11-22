---
id: architecture-era/transformer
type: architecture-era
slug: transformer
schema_version: 1.0
title: "The Transformer"
name: "Transformer"
era: scaling
era_range: "2017–present"
year: 2017
family: llm
core_innovation: "Replace recurrence and convolution with attention, so every position can attend to every other position and computation within a sequence runs in parallel."
description: "A sequence model built solely on attention mechanisms, with no recurrence or convolution; BERT and GPT are built from its encoder and decoder."
key_features: ["Self-attention", "Multi-head attention", "Positional encoding", "Position-wise feed-forward sub-layer", "Parallel computation across positions"]
key_models: ["Transformer (2017)", "BERT (2018)", "GPT (2018)", "T5 (2019)"]
paper_title: "Attention Is All You Need (Vaswani et al., 2017)"
paper_url: "https://arxiv.org/abs/1706.03762"
influenced_by: [architecture-era/seq2seq, architecture-era/attention-additive]
led_to: [architecture-era/bert, architecture-era/gpt, architecture-era/vision-transformer]

eli5: "A transformer reads all the words at once instead of one at a time. For every word it asks 'which other words should I pay attention to?' and mixes in their meaning. Stack that trick a few dozen times and the model builds a rich understanding of the whole sentence."

analogy:
  title: "A room full of people comparing notes"
  body: "Imagine every word in a sentence is a person in a room holding a note. Instead of whispering down a line — slow, and the message decays; that was the RNN — everyone raises their note at once. Each person looks around, decides whose notes are most relevant to them, and copies a weighted blend into their own. After a few rounds, every note carries the context of the whole room. That simultaneous 'everyone looks at everyone' step is self-attention."

narrative:
  motivation: "By 2017 the leading sequence models were recurrent networks (RNNs, LSTMs) that processed a sentence one token at a time. That sequential dependency precluded parallelization within a training example, and information between distant tokens had to pass through many steps. Attention was already used alongside these recurrent networks; the open question was whether attention alone could do the whole job."
  approach: "Remove recurrence and convolution entirely and build the model from attention plus position-wise feed-forward layers. If every token can attend directly to every other token, the path between any two positions has constant length and the whole sequence can be processed in parallel."
  solution: "Self-attention lets each token compute a relevance score against every other token and pull in a weighted mix of their values. Multiple 'heads' run in parallel so the model can attend to information from different representation subspaces at once. Because attention alone has no sense of order, a positional encoding is added to the inputs. Blocks of attention + feed-forward, wrapped in residual connections and layer normalization, are stacked into an encoder and a decoder."
  outcome: "On WMT 2014 translation the authors report 28.4 BLEU (English–German) and 41.8 BLEU (English–French), the latter after 3.5 days on eight GPUs, a small fraction of the training cost of earlier models. BERT (encoder), GPT (decoder) and the Vision Transformer (images) are built from it."

build_steps:
  - { title: "1. Embed the tokens", detail: "Turn each token into a vector (its learned meaning). This is the residual stream every later layer reads from and writes back to." }
  - { title: "2. Add position", detail: "Attention is order-blind, so add a positional signal (sine and cosine functions in the original; rotary positional embeddings (RoPE) in LLaMA, for example) so the model knows word 3 came before word 7." }
  - { title: "3. Project to Q, K, V", detail: "From each token vector compute three views: a Query (what am I looking for?), a Key (what do I offer?), and a Value (what will I hand over if chosen?)." }
  - { title: "4. Score and mix (self-attention)", detail: "Each Query dot-products with every Key to get relevance scores, softmax turns them into weights, and each token receives a weighted sum of Values. Now every token carries context from the whole sequence." }
  - { title: "5. Use many heads", detail: "Run steps 3–4 several times in parallel with different learned projections (eight heads in the original), then concatenate, so the model can attend to information from different representation subspaces." }
  - { title: "6. Feed-forward + residual + norm", detail: "Each position passes through the same feed-forward network; in the original it is two linear transformations with a ReLU in between (512 → 2048 → 512). Each sub-layer is wrapped in a residual connection followed by layer normalization." }
  - { title: "7. Stack N blocks", detail: "Repeat the attention + feed-forward block: six times in the encoder and six in the decoder of the original. Each decoder block also masks its self-attention so a position sees only earlier ones, and attends over the encoder's output." }

key_equation:
  latex: "\\mathrm{Attention}(Q,K,V)=\\mathrm{softmax}\\!\\left(\\frac{QK^{\\top}}{\\sqrt{d_k}}\\right)V"
  plain: "Score how well each query matches each key (QKᵀ), scale the scores so they don't blow up, turn them into probabilities with softmax, then take that weighted average of the values. In words: pay attention to the tokens whose keys best match my query, and copy a blend of their values."
  terms:
    - { symbol: "Q", meaning: "Queries — what each token is looking for" }
    - { symbol: "K", meaning: "Keys — what each token offers as a match target" }
    - { symbol: "V", meaning: "Values — the content each token contributes if attended to" }
    - { symbol: "d_k", meaning: "key dimension; dividing by its square root keeps the scores in a stable range" }

strengths:
  - "More parallelizable than recurrent models; the authors report significantly less training time."
  - "A constant number of sequential operations connects any two positions, against O(n) for a recurrent layer."
  - "For Transformer language models, Kaplan et al. (2020) report loss falling as a power law in model size, data and compute."
  - "The same architecture applies to images when patches are treated as tokens (Vision Transformer)."

weaknesses:
  - "Self-attention costs O(n²·d) per layer, quadratic in sequence length n, making very long contexts expensive."
  - "No built-in notion of order: position information must be injected."
  - "In vision, the Vision Transformer authors report it trails comparable ResNets when trained on mid-sized datasets such as ImageNet without strong regularization."

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
  - { name: "Transformer", year: 2017, note: "The original: an attention-only model that outperformed earlier translation systems on WMT 2014 with less training time." }
  - { name: "BERT", year: 2018, note: "Took the encoder and pretrained it bidirectionally, reporting new state-of-the-art results on eleven language tasks." }
  - { name: "GPT", year: 2018, note: "Took the decoder (12 layers, masked self-attention) for generative pretraining followed by fine-tuning on each task." }
  - { name: "T5", year: 2019, note: "Kept the full encoder-decoder and cast every NLP task as text-to-text." }
  - { name: "Vision Transformer (ViT)", year: 2020, note: "Applied the same architecture to images by treating patches as tokens." }

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
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.98 }
  - { url: "https://jalammar.github.io/illustrated-transformer/", title: "The Illustrated Transformer (Jay Alammar)", trust: 0.9 }
  - { url: "https://nlp.seas.harvard.edu/annotated-transformer/", title: "The Annotated Transformer (Harvard NLP)", trust: 0.9 }
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
Recurrent models read a sentence one token at a time, carrying a hidden state forward. That
sequential dependency **forbids parallelism within a sequence**: step 50 cannot start until step 49
finishes.

The Transformer (Vaswani et al., 2017) **discards recurrence and convolution**. Every token is
compared against every other token directly, so any two positions are connected by a constant number
of operations and the whole sequence is processed in parallel.

## Key Ideas
- **Self-attention is a soft lookup.** Each token issues a query, matches it against every key, and
  retrieves a weighted blend of values.
- **Multiple heads attend to different subspaces.** The authors report that individual heads learn
  different tasks, many related to the syntactic and semantic structure of sentences.
- **Position is added, not assumed.** Self-attention has no notion of order, so positional encodings
  are added to the input embeddings.
- **Attention plus feed-forward, stacked.** Each layer pairs attention with a position-wise
  feed-forward network, both wrapped in residual connections and normalization.
