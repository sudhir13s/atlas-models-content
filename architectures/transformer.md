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
  - { title: "7. Stack N blocks", detail: "Repeat the attention + feed-forward block dozens of times. Depth is what turns 'mixing context' into genuine understanding." }

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

diagram: |
  flowchart TD
    T["Tokens"] --> E["Token embeddings"]
    P["Positional encoding"] --> ADD["＋"]
    E --> ADD
    ADD --> MHA["Multi-Head Self-Attention"]
    MHA --> R1["Add & Norm"]
    R1 --> FF["Feed-Forward MLP"]
    FF --> R2["Add & Norm"]
    R2 --> N{"Repeat × N"}
    N -->|next block| MHA
    N -->|final| OUT["Output representations"]
diagram_caption: "One transformer block: attention mixes context across tokens, the feed-forward layer processes each token, and the whole block repeats N times."

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
