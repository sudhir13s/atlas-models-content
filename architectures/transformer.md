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
core_innovation: "Replace recurrence with self-attention so every token can look at every other token in one parallel step — enabling scale, long-range context, and the entire modern LLM era."
description: "The attention-only architecture that replaced RNNs and became the substrate for BERT, GPT, and virtually every modern large model."
key_features: ["Self-attention", "Multi-head attention", "Positional encoding", "Fully parallel training", "Encoder-decoder stack"]
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
  motivation: "By 2017 the best sequence models were recurrent networks (RNNs/LSTMs) that processed a sentence one token at a time. That sequential dependency made training slow — you cannot parallelize across time — and made long-range links fragile: information from token 1 had to survive being passed through 50 steps to reach token 50. Attention had already been bolted onto these models as a helper; the open question was whether attention alone could do the whole job."
  approach: "The bet was radical: throw away recurrence and convolution entirely and build the model out of nothing but attention plus ordinary feed-forward layers. If every token can directly attend to every other token, distance stops mattering and the whole sequence can be processed in parallel."
  solution: "Self-attention lets each token compute a relevance score against every other token and pull in a weighted mix of their values. Multiple 'heads' run in parallel so the model can attend to different relationships at once (syntax, coreference, topic). Because attention alone has no sense of order, a positional encoding is added to the inputs. Blocks of attention + feed-forward, wrapped in residual connections and layer normalization, are stacked into an encoder and a decoder."
  outcome: "The Transformer matched or beat recurrent translation systems at a fraction of the training cost — and, more importantly, it scaled. Because it parallelizes and its capacity grows smoothly with depth and width, it became the universal backbone: BERT (understanding), GPT (generation), ViT (vision), and every frontier LLM since are transformers. It is the single most consequential architecture of the modern era."

build_steps:
  - { title: "1. Embed the tokens", detail: "Turn each token into a vector (its learned meaning). This is the residual stream every later layer reads from and writes back to." }
  - { title: "2. Add position", detail: "Attention is order-blind, so add a positional signal (sinusoidal in the original, rotary positional embedding (RoPE) in modern LLMs) so the model knows word 3 came before word 7." }
  - { title: "3. Project to Q, K, V", detail: "From each token vector compute three views: a Query (what am I looking for?), a Key (what do I offer?), and a Value (what will I hand over if chosen?)." }
  - { title: "4. Score and mix (self-attention)", detail: "Each Query dot-products with every Key to get relevance scores, softmax turns them into weights, and each token receives a weighted sum of Values. Now every token carries context from the whole sequence." }
  - { title: "5. Use many heads", detail: "Run steps 3–4 several times in parallel with different learned projections, then concatenate. Different heads specialize in different relationships." }
  - { title: "6. Feed-forward + residual + norm", detail: "Each token passes through a small multi-layer perceptron (MLP) for extra nonlinear processing; residual connections and layer normalization keep gradients stable so the stack can go deep." }
  - { title: "7. Stack N blocks", detail: "Repeat the attention + feed-forward block: six times in the encoder and six in the decoder of the original, dozens of times in modern models. Each decoder block also masks its self-attention so a position sees only earlier ones, and attends over the encoder's output. Depth is what turns 'mixing context' into genuine understanding." }

key_equation:
  latex: "\\mathrm{Attention}(Q,K,V)=\\mathrm{softmax}\\!\\left(\\frac{QK^{\\top}}{\\sqrt{d_k}}\\right)V"
  plain: "Score how well each query matches each key (QKᵀ), scale the scores so they don't blow up, turn them into probabilities with softmax, then take that weighted average of the values. In words: pay attention to the tokens whose keys best match my query, and copy a blend of their values."
  terms:
    - { symbol: "Q", meaning: "Queries — what each token is looking for" }
    - { symbol: "K", meaning: "Keys — what each token offers as a match target" }
    - { symbol: "V", meaning: "Values — the content each token contributes if attended to" }
    - { symbol: "d_k", meaning: "key dimension; dividing by its square root keeps the scores in a stable range" }

strengths:
  - "Fully parallel across the sequence — trains far faster than RNNs on modern GPUs/TPUs."
  - "Direct token-to-token paths, so long-range dependencies are as easy to learn as short ones."
  - "Scales smoothly and predictably with depth, width, data, and compute."
  - "One architecture generalizes across text, vision, audio, and code."

weaknesses:
  - "Attention cost grows quadratically with sequence length (O(n²)), making very long contexts expensive."
  - "No built-in notion of order — position must be injected, and getting it right (e.g. RoPE) matters."
  - "Data- and compute-hungry; small-data regimes often still favor convolutional or recurrent priors."

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
notable_models:
  - { name: "Transformer", year: 2017, note: "The original — proved attention alone beats recurrence at machine translation, in less training time." }
  - { name: "BERT", year: 2018, note: "Took the encoder half and showed bidirectional pretraining gives state-of-the-art language understanding." }
  - { name: "GPT", year: 2018, note: "Took the decoder half and showed autoregressive pretraining scales into a general-purpose generator." }
  - { name: "T5", year: 2019, note: "Kept the full encoder-decoder and cast every NLP task as text-to-text." }
  - { name: "Vision Transformer (ViT)", year: 2020, note: "Proved the same architecture works on images by treating patches as tokens." }

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
updated: 2026-11-19
sources:
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.98 }
  - { url: "https://jalammar.github.io/illustrated-transformer/", title: "The Illustrated Transformer (Jay Alammar)", trust: 0.9 }
  - { url: "https://nlp.seas.harvard.edu/annotated-transformer/", title: "The Annotated Transformer (Harvard NLP)", trust: 0.9 }
variants:
- name: Decoder-only (Autoregressive)
  subtitle: Causal attention · next-token prediction
  models:
  - GPT-2/3/4/4o
  - Claude
  - Gemini
  - Llama 1-4
  - Mistral/Mixtral
  - Qwen
  - DeepSeek
  - Phi
  - Gemma
  - Command R+
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
Recurrent models read a sentence the way you read aloud — one word at a time, carrying a memory
forward. That memory is a bottleneck: it blurs over long distances and forbids parallelism, because
step 50 cannot start until step 49 finishes. The Transformer discards recurrence entirely. Every
token is compared against every other token directly, in one parallel operation, so *distance no
longer costs anything* and the whole sequence trains at once. This is why the architecture scaled
where its predecessors stalled.

## Key Ideas
- **Self-attention is a soft lookup.** Each token issues a query, matches it against every key, and
  retrieves a weighted blend of values — a differentiable, learned "search" over the sequence.
- **Multiple heads see multiple relationships.** One head might track subject–verb agreement, another
  long-range coreference; concatenating them gives a richer mix than any single attention pattern.
- **Position is added, not assumed.** Because attention is a set operation, order is injected via
  positional encodings — the one thing self-attention cannot infer on its own.
- **Depth plus residuals is the engine.** Stacking many attention + feed-forward blocks, kept stable
  by residual connections and normalization, is what converts context-mixing into understanding — and
  what keeps paying off as models grow.
