---
id: architecture-era/llama-recipe
type: architecture-era
slug: llama-recipe
schema_version: 1.0
title: "The LLaMA Recipe"
name: "LLaMA (Modern Open LLM Recipe)"
era: post-transformer
era_range: "2023–2026"
year: 2023
family: llm
core_innovation: "A decoder-only transformer with pre-normalisation (RMSNorm), SwiGLU activations and rotary position embeddings (RoPE), trained on more tokens than compute-optimal and released to the research community."
description: "The decoder-only transformer recipe of Meta's LLaMA paper (2023) and its Llama 2 and Llama 3 successors."
key_features: ["Pre-normalisation with RMSNorm", "SwiGLU activation in the feed-forward layer", "Rotary position embeddings (RoPE) instead of absolute position embeddings", "Grouped-query attention from Llama 2 70B onward", "Smaller models trained on more tokens, for cheaper inference", "Weights released"]
key_models: ["LLaMA (2023)", "Llama 2 (2023)", "Llama 3 (2024)"]
paper_title: "LLaMA: Open and Efficient Foundation Language Models"
paper_url: "https://arxiv.org/abs/2302.13971"
influenced_by: [architecture-era/transformer, architecture-era/scaling-laws]
led_to: [architecture-era/mixture-of-experts, architecture-era/mamba-ssm]

eli5: "LLaMA took the standard text-predicting transformer and swapped in three improvements that other papers had proposed. It trained fairly small models on a very large amount of text, so they are cheap to run, and shared the weights with researchers."

analogy:
  title: "A cookbook of tested ingredients"
  body: "Several cooks had each found one improvement: a better dough, a better oven setting, a better sauce. LLaMA combined three of them in one recipe (RMSNorm, SwiGLU and RoPE), cooked smaller portions for longer instead of bigger ones, and handed the recipe and the result to other researchers."

narrative:
  motivation: "Chinchilla's scaling laws say how to split a fixed training budget between model size and data, but the LLaMA authors note this ignores the cost of running the model. For a target quality, a smaller model trained longer is cheaper at inference."
  approach: "Train models from 7B to 65B parameters on 1 to 1.4 trillion tokens of publicly available data only, using a transformer with changes taken from earlier models: pre-normalisation from GPT-3, SwiGLU from PaLM and rotary embeddings from GPT-Neo."
  solution: "Each sub-layer's input is normalised with RMSNorm, the feed-forward ReLU is replaced by SwiGLU with a hidden size of 2/3·4d, and absolute position embeddings are replaced by RoPE at every layer. Llama 2 added grouped-query attention to its 70B model; Llama 3 uses it in all sizes, with a 128K-token vocabulary."
  outcome: "The authors report that LLaMA-13B outperforms GPT-3 (175B) on most benchmarks and that LLaMA-65B is competitive with Chinchilla-70B and PaLM-540B. The models were released to the research community."

build_steps:
  - { title: "1. Start decoder-only", detail: "Use a decoder-only transformer: stacked blocks of causal self-attention and a feed-forward network, trained to predict the next token." }
  - { title: "2. Pre-normalise with RMSNorm", detail: "Normalise the input of each sub-layer instead of its output, for training stability, using RMSNorm (Zhang and Sennrich, 2019), which rescales by the root mean square without re-centring." }
  - { title: "3. Use SwiGLU in the feed-forward layer", detail: "Replace the ReLU non-linearity with the SwiGLU activation (Shazeer, 2020), with a hidden dimension of 2/3·4d instead of 4d." }
  - { title: "4. Encode position with RoPE", detail: "Remove absolute position embeddings and apply rotary position embeddings (Su et al., 2021) at each layer, so attention scores depend on relative position." }
  - { title: "5. Grouped-query attention (Llama 2 70B, Llama 3)", detail: "Groups of query heads share one key/value head, which shrinks the key-value cache at inference." }
  - { title: "6. Train past compute-optimal and release", detail: "Train smaller models on more tokens than Chinchilla recommends (the authors note a 7B model still improves after 1T tokens), then release the weights." }

key_equation:
  latex: "\\mathcal{L}(\\theta) = -\\sum_{t=1}^{T} \\log P_\\theta\\big(x_t \\mid x_{<t}\\big)"
  plain: "LLaMA keeps the standard next-token objective: maximise the probability of each token given the tokens before it. The changes are in the architecture blocks and the amount of training data, not in the loss."
  terms:
    - { symbol: "𝓛(θ)", meaning: "the training loss (negative log-likelihood) minimised over the model parameters θ" }
    - { symbol: "x_t", meaning: "the actual token at position t in the training text" }
    - { symbol: "x_{<t}", meaning: "all tokens before position t, the context the model conditions on" }
    - { symbol: "P_θ(x_t | x_{<t})", meaning: "the model's predicted probability of the correct next token given the context" }
    - { symbol: "T", meaning: "the sequence length (number of tokens the loss is summed over)" }

strengths:
  - "Built for cheap inference: smaller models trained on more tokens."
  - "Trained only on publicly available data."
  - "The authors report LLaMA-13B outperforming GPT-3 (175B) on most benchmarks while being ten times smaller."
  - "Grouped-query attention (Llama 2 70B, Llama 3) reduces key-value cache size at inference."

weaknesses:
  - "A combination of existing techniques rather than a new architecture."
  - "Still a dense transformer: attention cost grows quadratically with sequence length."
  - "Weights ship under Meta's own licences rather than a standard open-source licence."
  - "Training past the compute-optimal point spends more training compute for a given model size."

diagram: |
  flowchart TD
    IN["Token embedding"] --> N1["RMSNorm (pre-norm)"]
    N1 --> ATT["Causal self-attention with RoPE<br/>(grouped-query from Llama 2 70B)"]
    ATT --> A1["Add (residual)"]
    IN --> A1
    A1 --> N2["RMSNorm (pre-norm)"]
    N2 --> FF["SwiGLU feed-forward"]
    FF --> A2["Add (residual)"]
    A1 --> A2
    A2 --> REP{"Repeat × N"}
    REP -->|"next block"| N1
    REP -->|"final"| OUT["Next-token prediction"]
diagram_caption: "One LLaMA block: RMSNorm before each sub-layer, causal self-attention with rotary position embeddings, and a SwiGLU feed-forward layer, wrapped in residual connections and repeated N times."

notable_models:
  - { name: "LLaMA", year: 2023, note: "7B to 65B models trained on public data; the authors report the 13B model outperforming GPT-3 (175B) on most benchmarks." }
  - { name: "Llama 2", year: 2023, note: "7B to 70B models with fine-tuned chat versions; the 70B model uses grouped-query attention." }
  - { name: "Llama 3", year: 2024, note: "Pretrained on over 15 trillion tokens, with a 128K-token vocabulary and grouped-query attention in all sizes." }
  - { name: "Mistral 7B", year: 2023, note: "Uses grouped-query attention and sliding-window attention; its authors report it outperforming Llama 2 13B on all evaluated benchmarks." }

resources:
  - { kind: paper, title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", url: "https://arxiv.org/abs/2302.13971" }
  - { kind: paper, title: "Llama 2: Open Foundation and Fine-Tuned Chat Models (Touvron et al., 2023)", url: "https://arxiv.org/abs/2307.09288" }
  - { kind: paper, title: "The Llama 3 Herd of Models (Grattafiori et al., 2024)", url: "https://arxiv.org/abs/2407.21783" }

learn_first:
  - { label: "The Transformer block (what the recipe modifies)", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "GPT & decoder-only autoregressive training", href: "/models/architecture/gpt", tag: "Architecture" }
  - { label: "Scaling laws and compute-optimal training (Chinchilla)", href: "/models/architecture/scaling-laws", tag: "Concept" }

tags: [llama, open-weights, rope, rmsnorm, swiglu, gqa, llm]
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/2302.13971", title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2307.09288", title: "Llama 2: Open Foundation and Fine-Tuned Chat Models (Touvron et al., 2023)", trust: 0.9 }
  - { url: "https://github.com/meta-llama/llama/blob/main/MODEL_CARD.md", title: "Llama 2 model card (Meta)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models (Grattafiori et al., 2024)", trust: 0.95 }
  - { url: "https://github.com/meta-llama/llama3/blob/main/MODEL_CARD.md", title: "Llama 3 model card (Meta)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2310.06825", title: "Mistral 7B (Jiang et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1910.07467", title: "Root Mean Square Layer Normalization (Zhang and Sennrich, 2019)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2002.05202", title: "GLU Variants Improve Transformer (Shazeer, 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2104.09864", title: "RoFormer: Enhanced Transformer with Rotary Position Embedding (Su et al., 2021)", trust: 0.95 }
---

## What Changed
LLaMA (Touvron et al., 2023) changed the original transformer in three places, each taken from an
earlier model:

- **Pre-normalisation with RMSNorm** (from GPT-3): each sub-layer's input is normalised, for training stability.
- **SwiGLU** (from PaLM) replaces the ReLU in the feed-forward layer.
- **Rotary position embeddings** (from GPT-Neo) replace absolute position embeddings.

The authors trained smaller models on more tokens than Chinchilla recommends, because such models are
cheaper at inference. Llama 2 added **grouped-query attention** to its 70B model; Llama 3 uses it in all sizes.

## Key Ideas
- **RMSNorm, pre-norm**: normalise by root mean square (no mean subtraction) before each sub-layer;
  its authors report comparable quality to LayerNorm with 7% to 64% lower running time.
- **SwiGLU feed-forward**: a gated unit, $\text{Swish}(xW)\odot xV$; Shazeer (2020) reports quality
  gains over ReLU and GELU in the transformer feed-forward layer.
- **RoPE**: rotate query and key vectors by position so attention scores depend on relative offset.
- **Grouped-query attention**: groups of query heads share key/value heads, reducing key-value cache memory.
- **Inference-oriented training**: a smaller model trained on more tokens, released to researchers.
