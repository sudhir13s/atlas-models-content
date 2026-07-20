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
core_innovation: "A consolidated, efficient decoder-only recipe — RMSNorm, SwiGLU, RoPE, GQA — trained data-optimally and released openly."
description: "The reference open-weights transformer recipe that standardized modern LLM design and catalyzed the open ecosystem."
key_features: ["Pre-norm with RMSNorm", "SwiGLU feed-forward", "Rotary position embeddings (RoPE)", "Grouped-query attention (Llama-2)", "Chinchilla-informed data scaling", "Open weights"]
key_models: ["LLaMA (2023)", "Llama-2 (2023)", "Llama-3 (2024)"]
paper_title: "LLaMA: Open and Efficient Foundation Language Models"
paper_url: "https://arxiv.org/abs/2302.13971"
influenced_by: [architecture-era/transformer, architecture-era/scaling-laws]
led_to: [architecture-era/mixture-of-experts, architecture-era/mamba-ssm]

eli5: "By 2023 all the good tricks for building a language model were scattered across dozens of papers. LLaMA gathered the best ones into one clean cookbook, trained the models on lots of data instead of just making them big, and gave the weights away — so anyone could cook the same dish and start improving it."

analogy:
  title: "The definitive open-source cookbook"
  body: "For years, the best recipe for a great dish lived in a hundred different chefs' private notebooks — one had the perfect dough, another the ideal oven temperature, a third the trick for the sauce. LLaMA is the chef who tested them all, wrote down the single best combination in a clean, reproducible cookbook, and published it for free. RMSNorm, SwiGLU, RoPE, and grouped-query attention are the chosen ingredients; 'train longer on more data' is the technique; open weights are the free cookbook that let a whole community start cooking and remixing."

narrative:
  motivation: "By 2023 the transformer's best-practice details were scattered: normalization choices in one paper, better activations in another, position encodings in a third. There was no single, clean, reproducible template for a strong decoder-only model — and the best models were closed, so the open community couldn't build on them."
  approach: "Consolidate, don't invent. LLaMA picked the refinements that consistently helped and assembled them into one efficient decoder-only recipe — then took the Chinchilla scaling lesson seriously by training relatively small models on far more tokens than was fashionable, buying quality through data rather than sheer size."
  solution: "The recipe swaps four blocks into the standard transformer: RMSNorm (a cheaper normalization) applied pre-norm for stable deep training; SwiGLU gated feed-forward layers that beat plain ReLU MLPs at equal compute; RoPE (rotary position embeddings) that encode relative position by rotating query/key vectors and generalize better to longer contexts; and, from Llama-2, grouped-query attention (GQA), which shares key/value heads across groups of query heads to shrink the KV-cache and speed inference. Then it trained data-optimally and, crucially, released the weights."
  outcome: "A 13B LLaMA rivaled the 175B GPT-3, proving data-optimal training beats raw size. More importantly, open weights ignited the open-model ecosystem — a wave of fine-tunes, quantizations, and derivatives. This exact block set became the de facto template most open-weight LLMs now follow, and its efficient backbone is what later MoE and state-space variants extend."

build_steps:
  - { title: "1. Start decoder-only", detail: "Use the GPT-style decoder-only transformer: stacked blocks of causal self-attention and a feed-forward network, trained to predict the next token." }
  - { title: "2. Normalize with RMSNorm, pre-norm", detail: "Replace LayerNorm with RMSNorm (scale by root-mean-square, skip mean subtraction) and apply it before each sublayer. Cheaper to compute and more stable for deep stacks." }
  - { title: "3. Swap in SwiGLU feed-forward", detail: "Replace the ReLU MLP with a gated SwiGLU layer — a Swish-gated projection that reliably improves quality per parameter at the same compute." }
  - { title: "4. Encode position with RoPE", detail: "Instead of adding absolute position embeddings, rotate the query and key vectors by an angle that depends on position. Attention scores then depend on relative offset, which generalizes better to longer contexts." }
  - { title: "5. Add grouped-query attention (Llama-2 onward)", detail: "Let groups of query heads share one set of key/value heads. This shrinks the KV-cache and speeds up inference with minimal quality loss." }
  - { title: "6. Train data-optimally and release", detail: "Follow the Chinchilla lesson: train smaller models on far more tokens rather than chasing parameter count — then publish the weights so the community can fine-tune and extend." }

key_equation:
  latex: "\\mathcal{L}(\\theta) = -\\sum_{t=1}^{T} \\log P_\\theta\\big(x_t \\mid x_{<t}\\big)"
  plain: "LLaMA keeps the standard next-token objective — maximize the probability of each real token given everything before it — unchanged. Its innovation isn't the loss; it's the efficient architecture blocks (RMSNorm, SwiGLU, RoPE, GQA) and the data-optimal training that make this same objective produce a far stronger model per unit of compute. This equation is the objective every LLaMA-style model optimizes."
  terms:
    - { symbol: "𝓛(θ)", meaning: "the training loss (negative log-likelihood) minimized over the model parameters θ" }
    - { symbol: "x_t", meaning: "the actual token at position t in the training text" }
    - { symbol: "x_{<t}", meaning: "all tokens before position t — the context the model conditions on" }
    - { symbol: "P_θ(x_t | x_{<t})", meaning: "the model's predicted probability of the correct next token given the context" }
    - { symbol: "T", meaning: "the sequence length (number of tokens the loss is summed over)" }

strengths:
  - "Efficient by design — RMSNorm, SwiGLU, RoPE, and GQA give more quality per parameter and per FLOP."
  - "Open weights catalyzed a huge ecosystem of fine-tunes, quantizations, and derivatives."
  - "Data-optimal training: a 13B model rivaling a 175B one proves smaller-but-longer-trained wins."
  - "A clean, reproducible template that became the shared standard for open-weight LLMs."

weaknesses:
  - "Architecturally a consolidation, not a fundamental breakthrough — it refines the transformer rather than replacing it."
  - "Still a dense transformer: attention remains quadratic in sequence length."
  - "Early open-weight releases carried restrictive licenses and required careful safety tuning for chat use."
  - "Data-optimal training costs more tokens, so pretraining compute is high even for modest parameter counts."

diagram: |
  flowchart TD
    IN["Token + RoPE position"] --> N1["RMSNorm (pre-norm)"]
    N1 --> ATT["Grouped-query self-attention"]
    ATT --> A1["Add (residual)"]
    IN --> A1
    A1 --> N2["RMSNorm (pre-norm)"]
    N2 --> FF["SwiGLU feed-forward"]
    FF --> A2["Add (residual)"]
    A1 --> A2
    A2 --> REP{"Repeat × N"}
    REP -->|"next block"| N1
    REP -->|"final"| OUT["Next-token prediction"]
diagram_caption: "One LLaMA block: pre-norm RMSNorm, grouped-query attention with RoPE positions, and a SwiGLU feed-forward, wrapped in residuals and repeated N times. The four swaps over the vanilla transformer are what make it efficient."

notable_models:
  - { name: "LLaMA", year: 2023, note: "Consolidated the modern recipe and showed a 13B model rivaling 175B GPT-3, proving data-optimal training over raw scale." }
  - { name: "Llama-2", year: 2023, note: "Added grouped-query attention and a full open chat pipeline, cementing the recipe as the community default." }
  - { name: "Llama-3", year: 2024, note: "Scaled the same recipe with far more training tokens and a larger vocabulary, pushing open models near frontier quality." }
  - { name: "Mistral 7B", year: 2023, note: "Showed the recipe plus sliding-window attention could beat larger models, spreading the template beyond Meta." }

resources:
  - { kind: paper, title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", url: "https://arxiv.org/abs/2302.13971" }
  - { kind: paper, title: "Llama 2: Open Foundation and Fine-Tuned Chat Models (Touvron et al., 2023)", url: "https://arxiv.org/abs/2307.09288" }
  - { kind: blog, title: "The Llama 3 Herd of Models (Meta AI)", url: "https://ai.meta.com/research/publications/the-llama-3-herd-of-models/" }
  - { kind: video, title: "Let's reproduce GPT-2 / build the modern LLM stack (Andrej Karpathy)", url: "https://www.youtube.com/watch?v=l8pRSuU81PU" }

learn_first:
  - { label: "The Transformer block (what the recipe modifies)", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "GPT & decoder-only autoregressive training", href: "/models/architecture/gpt", tag: "Architecture" }
  - { label: "Scaling laws & the Chinchilla data-optimal lesson", href: "/models/architecture/scaling-laws", tag: "Concept" }

tags: [llama, open-weights, rope, rmsnorm, swiglu, gqa, llm]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2302.13971", title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2307.09288", title: "Llama 2: Open Foundation and Fine-Tuned Chat Models (Touvron et al., 2023)", trust: 0.9 }
---

## What Changed
By 2023 the transformer's best-practice details were scattered across many papers. LLaMA bundled them
into one clean, efficient, *reproducible* decoder-only recipe and — crucially — released the weights,
igniting the open-model ecosystem. The recipe is a set of well-chosen refinements over the original
transformer block: **RMSNorm** (a cheaper normalization) applied **pre-norm** for training stability;
**SwiGLU** gated feed-forward layers that outperform plain ReLU MLPs; **RoPE** (rotary position
embeddings) that encode relative position by rotating query/key vectors and generalise better to
longer contexts than learned absolute embeddings; and, in Llama-2, **grouped-query attention (GQA)**,
which shares key/value heads across groups of query heads to shrink the KV-cache and speed up
inference. It also took the Chinchilla lesson seriously, training smaller models on far more tokens so
that a 13B model could rival a 175B one. This combination became the de facto template that most
open-weight LLMs now follow, and its efficient backbone is what later MoE and state-space variants
extend.

## Key Ideas
- **RMSNorm, pre-norm** — normalise by root-mean-square (no mean subtraction) before each sublayer;
  cheaper and more stable for deep stacks than post-norm LayerNorm.
- **SwiGLU FFN** — a gated activation ($\text{Swish}(xW)\odot xV$) that consistently beats ReLU MLPs at
  equal compute, improving quality per parameter.
- **RoPE** — rotate query/key vectors by position so attention scores depend on *relative* offset,
  giving better length behaviour than absolute position embeddings.
- **Grouped-query attention** — share K/V projections across query-head groups to cut KV-cache memory
  and boost inference throughput with minimal quality loss.
- **Data-optimal + open** — train longer on more tokens per Chinchilla, and release weights so the
  community can fine-tune and extend the recipe.
