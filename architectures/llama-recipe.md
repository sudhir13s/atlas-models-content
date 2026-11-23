---
id: architecture-era/llama-recipe
type: architecture-era
slug: llama-recipe
schema_version: 2.7
title: "The LLaMA Recipe"
name: "LLaMA (Modern Open LLM Recipe)"
era: post-transformer
era_range: "2023–2026"
year: 2023
family: llm
core_innovation: "A decoder-only transformer with pre-normalisation (RMSNorm), SwiGLU activations and rotary position embeddings (RoPE), trained on more tokens than compute-optimal and released to the research community."
description: "A Transformer that predicts the next token from the tokens before it, built with three changes borrowed from earlier work (RMSNorm before each sub-layer, a SwiGLU feed-forward layer and rotary position embeddings); introduced in Meta's LLaMA paper (2023) and kept in Llama 2 and Llama 3."
key_features: ["Pre-normalisation with RMSNorm", "SwiGLU activation in the feed-forward layer", "Rotary position embeddings (RoPE) instead of absolute position embeddings", "Grouped-query attention in the 34B and 70B Llama 2 models and in every Llama 3 model", "Smaller models trained on more tokens, for cheaper inference", "Weights released"]
key_models: ["LLaMA (2023)", "Llama 2 (2023)", "Llama 3 (2024)"]
paper_title: "LLaMA: Open and Efficient Foundation Language Models"
paper_url: "https://arxiv.org/abs/2302.13971"
influenced_by: [architecture-era/transformer, architecture-era/scaling-laws]
led_to: [architecture-era/mamba-ssm]

analogy:
  title: "A recipe assembled from tested ingredients"
  body: "This is an analogy, not the mechanism. Several cooks had each published one improvement: a steadier way to mix (RMSNorm), a better sauce (SwiGLU) and a new way to mark the order of the courses (rotary embeddings). The LLaMA cooks did not invent a new dish; they combined these three in one recipe, cooked smaller portions for longer instead of bigger ones so each plate is cheaper to serve, and handed the result to other researchers. Unlike a kitchen, nothing here is chosen by taste: each change is a formula, shown with numbers in the worked example."

narrative:
  motivation: "**Compute-optimal training ignored the cost of running the model.** The paper is by **Touvron et al. (2023)** at Meta AI.\n\n- **The prior finding:** **Hoffmann et al. (2022)** showed that, for a fixed training budget, the best results come from smaller models trained on more data.\n- **The gap the authors name:** that objective disregards the **inference budget**, which matters when a model is served at scale.\n- **Their example:** Hoffmann et al. recommend a **10B** model on **200B** tokens, yet the authors find a **7B** model keeps improving **after 1T** tokens."
  approach: "**Train smaller models for longer, on public data only.**\n\n- **Sizes:** **7B, 13B, 33B and 65B** parameters.\n- **Data:** **1.0T** tokens for 7B and 13B, **1.4T** for 33B and 65B, using only publicly available data: CommonCrawl **67%**, C4 **15%**, GitHub, Wikipedia and books **4.5%** each, arXiv **2.5%**, Stack Exchange **2%**.\n- **Architecture:** the Transformer of **Vaswani et al. (2017)** with three changes taken from earlier models, each named with where the authors found it: pre-normalisation (**GPT-3**), SwiGLU (**PaLM**) and rotary embeddings (**GPT-Neo**)."
  solution: "**Three changes to the block, then standard training.**\n\n- **RMSNorm before each sub-layer:** the input of each sub-layer is normalised, not its output, using RMSNorm by **Zhang and Sennrich (2019)**.\n- **SwiGLU feed-forward:** the ReLU is replaced by SwiGLU by **Shazeer (2020)**, with an inner width of **2/3 · 4d**.\n- **Rotary embeddings:** absolute position embeddings are removed and RoPE by **Su et al. (2021)** is applied **at each layer**.\n- **Training:** AdamW (β₁ = **0.9**, β₂ = **0.95**), cosine schedule down to **10%** of the peak, weight decay **0.1**, **2,000** warm-up steps; the 65B model processed about **380 tokens per second per GPU** on **2,048** A100 GPUs, about **21 days** for 1.4T tokens.\n- **Llama 2 (2023):** **40%** more tokens (**2.0T**), context doubled to **4,096**, and **grouped-query attention (GQA)** in the 34B and 70B models.\n- **Llama 3 (2024):** GQA with **8 key-value heads** in every size, a **128,000**-token vocabulary and a RoPE base frequency of **500,000**."
  outcome: "**Results the authors report:**\n\n- **LLaMA-13B:** outperforms **GPT-3 (175B)** on most benchmarks while being more than **10×** smaller, and runs on a single V100 GPU for inference.\n- **LLaMA-65B:** competitive with **Chinchilla-70B** and **PaLM-540B**; it outperforms Chinchilla-70B on all reported common-sense benchmarks except BoolQ.\n- **Release:** the models were released to the research community under a **non-commercial** licence; Llama 2 and Llama 3 followed under a **custom commercial** licence.\n\n**What came next:**\n\n- **Llama 3 405B:** **126** layers, trained on **15.6T** tokens with a context window of up to **128K** tokens; its authors write that the architecture does not deviate significantly from Llama and Llama 2."

key_equation:
  latex: "q_m^{\\top} k_n = \\left(R_{m\\theta}\\, W_q x_m\\right)^{\\top}\\left(R_{n\\theta}\\, W_k x_n\\right) = x_m^{\\top} W_q^{\\top} R_{(n-m)\\theta}\\, W_k x_n"
  plain: "In words: rotary position embedding turns the query of the token at position m by the angle m·θ and the key of the token at position n by n·θ, then takes their dot product as the attention score. Two rotations combine into one rotation by the difference, so the score depends on the words and on how far apart they are (n − m), not on where they sit in the text. This is equation 16 of the RoFormer paper; part 3 of the worked example checks it with numbers."
  terms:
    - { symbol: "x_m, x_n", meaning: "the vectors of the tokens at positions m and n, after RMSNorm" }
    - { symbol: "W_q, W_k", meaning: "the learned matrices that turn a token's vector into its query and its key" }
    - { symbol: "R_{m\\theta}", meaning: "a rotation by the angle m·θ, applied to each pair of numbers of the vector; each pair i has its own angle θᵢ = 10000^(−2(i−1)/d)" }
    - { symbol: "q_m^{\\top} k_n", meaning: "the attention score of the query at position m for the key at position n, before division by the square root of the head size" }
    - { symbol: "n - m", meaning: "the distance between the two tokens: the only position information left in the score" }

strengths:
  - "**Cheaper to run for its quality:** the authors report that **LLaMA-13B** outperforms **GPT-3 (175B)** on most benchmarks while being more than **10×** smaller, and that it runs on a **single V100 GPU** at inference."
  - "**Public data only:** every training source is publicly available (CommonCrawl **67%**, C4 **15%**, and smaller shares of GitHub, Wikipedia, books, arXiv and Stack Exchange), which the authors call compatible with open-sourcing."
  - "**A lighter normalisation:** the RMSNorm authors report performance comparable to LayerNorm with running time reduced by **7% to 64%** across their models."
  - "**A better feed-forward layer:** with parameters and computation matched, Shazeer reports a held-out log-perplexity of **1.636** for SwiGLU against **1.677** for ReLU after **524,288** training steps."
  - "**A smaller key-value cache:** Llama 3 uses grouped-query attention with **8 key-value heads**, which its authors adopt to speed up inference and reduce the size of the key-value cache during decoding."

weaknesses:
  - "**Little new architecture, by design:** the LLaMA authors describe their changes as improvements proposed in earlier work, and the Llama 3 authors write that their gains come **primarily from data quality and scale**, not from architecture."
  - "**Attention still grows with the square of length:** self-attention costs **O(n²·d)** per layer (Vaswani et al., Table 1); LLaMA was trained with a **2,048**-token context and Llama 2 with **4,096**."
  - "**Bespoke licences:** the LLaMA model card gives a **non-commercial bespoke licence**; the Llama 2 and Llama 3 model cards give a **custom commercial licence** with an Acceptable Use Policy."
  - "**More training compute per model:** the authors acknowledge that a larger model may be **cheaper to train** to a given level of performance; training the 65B model on 1.4T tokens took about **21 days** on **2,048** A100 GPUs."
  - "**An empirical gain, not an explained one:** Shazeer writes that he offers **no explanation** for why the GLU variants work."

visual:
  kind: rotary
  query: [1, 0]
  key: [2, 1]
  angle_degrees: 90
  positions:
    - { query: 1, key: 3 }
    - { query: 2, key: 4 }
    - { query: 1, key: 2 }
  caption: "Part 3 of the worked example: the query and the key turned by 90° per position, for the three pairs of positions it works through, with each score computed from the turned arrows. The first two pairs are 2 apart and score the same; the third is 1 apart and scores differently. The vectors and the angle are an example, not from a trained model."

comparison:
  before: "Original Transformer (2017)"
  after: "LLaMA (2023)"
  rows:
    - { aspect: "Where normalisation is applied", before: "To the output of each sub-layer: LayerNorm(x + Sublayer(x))", after: "To the input of each sub-layer (pre-normalisation), for training stability", source: "https://arxiv.org/abs/2302.13971" }
    - { aspect: "Normalisation function", before: "LayerNorm: subtract the mean, divide by the standard deviation, then rescale", after: "RMSNorm: divide by the root mean square only, then rescale; no mean is subtracted", source: "https://arxiv.org/abs/1910.07467" }
    - { aspect: "Feed-forward non-linearity", before: "ReLU between two weight matrices", after: "SwiGLU, with three weight matrices and an inner width of 2/3 · 4d", source: "https://arxiv.org/abs/2302.13971" }
    - { aspect: "How word order enters", before: "Absolute position embeddings added once to the input", after: "Rotary position embeddings applied at each layer", source: "https://arxiv.org/abs/2302.13971" }
    - { aspect: "Key and value heads (Llama 3)", before: "One key head and one value head for every query head", after: "8 key-value heads shared by 32 to 128 query heads (grouped-query attention)", source: "https://arxiv.org/abs/2407.21783" }

key_numbers:
  columns: ["Model", "Parameters", "Layers", "Vector width (d)", "Attention heads", "Training tokens"]
  rows:
    - { cells: ["LLaMA 7B (2023)", "6.7B", "32", "4,096", "32", "1.0T"], source: "https://arxiv.org/abs/2302.13971" }
    - { cells: ["LLaMA 13B (2023)", "13.0B", "40", "5,120", "40", "1.0T"], source: "https://arxiv.org/abs/2302.13971" }
    - { cells: ["LLaMA 33B (2023)", "32.5B", "60", "6,656", "52", "1.4T"], source: "https://arxiv.org/abs/2302.13971" }
    - { cells: ["LLaMA 65B (2023)", "65.2B", "80", "8,192", "64", "1.4T"], source: "https://arxiv.org/abs/2302.13971" }
    - { cells: ["Llama 3 8B (2024)", "8B", "32", "4,096", "32 query, 8 key-value", "about 15T (the pre-training corpus)"], source: "https://arxiv.org/abs/2407.21783" }
    - { cells: ["Llama 3 405B (2024)", "405B", "126", "16,384", "128 query, 8 key-value", "15.6T"], source: "https://arxiv.org/abs/2407.21783" }

misunderstandings:
  - misunderstanding: "LLaMA invented RMSNorm, SwiGLU and rotary position embeddings."
    truth: "The paper credits each change to earlier work and names the model that inspired it: pre-normalisation from **GPT-3**, using RMSNorm by **Zhang and Sennrich (2019)**; SwiGLU by **Shazeer (2020)**, as used in **PaLM**; rotary embeddings by **Su et al. (2021)**, as used in **GPT-Neo**. The recipe is the combination, together with how long the models were trained."
    source: "https://arxiv.org/abs/2302.13971"
  - misunderstanding: "Training a model on more tokens than the compute-optimal amount wastes compute."
    truth: "It spends more **training** compute, but the authors argue that the compute-optimal objective of Hoffmann et al. disregards the **inference** budget. For a target performance, a smaller model trained longer is cheaper to run. Their example: Hoffmann et al. recommend a **10B** model on **200B** tokens, while a **7B** model keeps improving **after 1T** tokens."
    source: "https://arxiv.org/abs/2302.13971"
  - misunderstanding: "Every Llama model uses grouped-query attention."
    truth: "The Llama 2 paper's Table 1 marks **no** grouped-query attention for any LLaMA (version 1) model or for Llama 2 7B and 13B. Only the **34B** and **70B** Llama 2 models use it, and the 34B model was not released. Llama 3 later used it in every size (see the key numbers)."
    source: "https://arxiv.org/abs/2307.09288"
  - misunderstanding: "The original LLaMA weights were released under an open-source licence."
    truth: "The LLaMA model card gives a **non-commercial bespoke licence**, and names **researchers** in natural language processing, machine learning and artificial intelligence as the primary intended users. Llama 2 and Llama 3 later moved to a custom licence that allows commercial use."
    source: "https://github.com/meta-llama/llama/blob/llama_v1/MODEL_CARD.md"

diagram: |
  flowchart TD
    IN["Token embedding"] --> N1["RMSNorm (pre-norm)"]
    N1 --> ATT["Causal self-attention with RoPE<br/>(grouped-query in larger models)"]
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
  - { name: "LLaMA", year: 2023, note: "7B to 65B models trained on public data only; the authors report the 13B model outperforming GPT-3 (175B) on most benchmarks." }
  - { name: "Llama 2", year: 2023, note: "7B, 13B and 70B models released with fine-tuned chat versions, trained on 2.0T tokens with a 4,096-token context; the 70B model uses grouped-query attention." }
  - { name: "Llama 3", year: 2024, note: "8B and 70B models pretrained on over 15 trillion tokens, with a 128K-token vocabulary and grouped-query attention in all sizes." }
  - { name: "Mistral 7B", year: 2023, note: "Uses grouped-query attention and sliding-window attention; its authors report it outperforming Llama 2 13B across all tested benchmarks, and released it under the Apache 2.0 licence." }

resources:
  - { kind: paper, title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", url: "https://arxiv.org/abs/2302.13971" }
  - { kind: paper, title: "Llama 2: Open Foundation and Fine-Tuned Chat Models (Touvron et al., 2023)", url: "https://arxiv.org/abs/2307.09288" }
  - { kind: paper, title: "The Llama 3 Herd of Models (Grattafiori et al., 2024)", url: "https://arxiv.org/abs/2407.21783" }
  - { kind: paper, title: "RoFormer: Enhanced Transformer with Rotary Position Embedding (Su et al., 2021)", url: "https://arxiv.org/abs/2104.09864" }

learn_first:
  - { label: "The Transformer block (what the recipe modifies)", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "GPT & decoder-only autoregressive training", href: "/models/architecture/gpt", tag: "Architecture" }
  - { label: "Scaling laws and compute-optimal training (Chinchilla)", href: "/models/architecture/scaling-laws", tag: "Concept" }

tags: [llama, open-weights, rope, rmsnorm, swiglu, gqa, llm]
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/2302.13971", title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1910.07467", title: "Root Mean Square Layer Normalization (Zhang and Sennrich, 2019)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2002.05202", title: "GLU Variants Improve Transformer (Shazeer, 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2104.09864", title: "RoFormer: Enhanced Transformer with Rotary Position Embedding (Su et al., 2021)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.98 }
  - { url: "https://arxiv.org/abs/2305.13245", title: "GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints (Ainslie et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2307.09288", title: "Llama 2: Open Foundation and Fine-Tuned Chat Models (Touvron et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models (Grattafiori et al., 2024)", trust: 0.95 }
  - { url: "https://github.com/meta-llama/llama/blob/llama_v1/MODEL_CARD.md", title: "LLaMA model card, version 1 (Meta)", trust: 0.9 }
  - { url: "https://github.com/meta-llama/llama/blob/main/MODEL_CARD.md", title: "Llama 2 model card (Meta)", trust: 0.9 }
  - { url: "https://github.com/meta-llama/llama3/blob/main/MODEL_CARD.md", title: "Llama 3 model card (Meta)", trust: 0.9 }
  - { url: "https://github.com/meta-llama/llama/blob/main/llama/model.py", title: "Llama reference implementation, model.py (Meta): RMSNorm, rotary embeddings, SwiGLU width rounding", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2310.06825", title: "Mistral 7B (Jiang et al., 2023)", trust: 0.95 }
---

## What Changed

LLaMA (Touvron et al., 2023) kept the Transformer and changed **three parts of its block**, each
taken from earlier work. It also changed **how long** the models were trained.

How to read the table above:

- **A sub-layer** is one of the two parts of a block: attention, then the feed-forward network.
- **Pre-normalisation:** the original Transformer normalised after adding a sub-layer's output;
  LLaMA normalises the input before each sub-layer, which the authors adopt for training stability.
- **RMSNorm** drops the mean subtraction of LayerNorm. Its authors hypothesise that re-scaling,
  not re-centring, is what makes LayerNorm work.
- **SwiGLU** multiplies two projections of the input, one passed through the Swish function, so
  it needs a third weight matrix; the inner width shrinks to 2/3 · 4d to compensate.
- **Rotary embeddings** move position from the input into every attention score.
- **Grouped-query attention** (GQA, Ainslie et al., 2023) lets groups of query heads share one key
  head and one value head; Llama 2 adopted it for its 34B and 70B models.

**Training length:** LLaMA-7B and 13B saw **1.0T** tokens and the larger two **1.4T**, where
Hoffmann et al. recommend a 10B model on 200B tokens, as the LLaMA paper quotes them.

## Key Ideas

- **RMSNorm, before each sub-layer:** divide by the root mean square, then rescale by learned gains.
- **SwiGLU feed-forward:** a Swish-gated product of two projections, then a third projection back.
- **RoPE:** rotate queries and keys by position so attention scores depend on distance.
- **Grouped-query attention (larger Llama 2 models, all Llama 3):** fewer key-value heads, a
  smaller key-value cache.
- **Inference-oriented training:** smaller models trained on more tokens, released to researchers.

## How It Works

The figure above is drawn from a catalog model built on this recipe; select a block to open it.
Follow it from the top. The numbers below are those of **LLaMA 7B** (paper Table 2 and Meta's
reference code).

A **token** is a piece of text, a word or part of a word. A **vector** is a list of numbers.

1. **Tokenizer.** Text is cut into tokens by **byte-pair encoding (BPE)**, using SentencePiece.
   - **Numbers** are split into single digits.
   - **Unknown characters** fall back to bytes, so any text can be encoded.
   - **Vocabulary:** 32,000 tokens in LLaMA and Llama 2; **128,000** in Llama 3.
2. **Token embedding.** Each token becomes a vector of **4,096** numbers.
3. **Positional encoding (RoPE).** Nothing is added to the vector here. The figure's RoPE block
   stands for the rotation applied **inside every attention layer**, to queries and keys only.
4. **RMSNorm (before attention).** The vector is divided by its root mean square and multiplied by
   a learned gain per number.
   - **Formula:** x̄ᵢ = xᵢ / RMS(x) · gᵢ, with RMS(x) = √(mean of xᵢ²).
   - **In the code:** a tiny constant is added under the square root to avoid dividing by zero.
5. **Attention (MHA, or GQA in larger models).** Queries, keys and values are computed from the
   normalised vector.
   - **Heads:** **32** heads of **128** numbers each in LLaMA 7B (32 × 128 = 4,096).
   - **Rotation:** each query and key is rotated by its position before the scores are taken
     (the key equation).
   - **Causal mask:** each token sees only itself and earlier tokens; scores are divided by
     √128 and turned into weights by softmax.
   - **GQA:** Llama 3 shares **8** key-value heads among all query heads.
6. **"+" (residual).** The attention output is added back to the block's input:
   h = x + Attention(RMSNorm(x)).
7. **RMSNorm, then the gated feed-forward (SwiGLU).** Three weight matrices, written w1, w3 and w2
   in the reference code: output = w2( Swish(w1·x) × w3·x ).
   - **Swish** (also called SiLU) is z · σ(z), where σ is the sigmoid.
   - **Inner width:** 2/3 · 4 · 4,096 ≈ 10,923, rounded up in the code to a multiple of 256:
     **11,008**.
8. **"+" (residual) and repeat.** The feed-forward output is added back too. Steps 4 to 8 form one
   block; LLaMA 7B stacks **32**.
9. **Final RMSNorm and output head.** A last RMSNorm, then one score per vocabulary entry; softmax
   turns the scores into probabilities for the next token.

## Worked Example

**The three changes on tiny numbers.** All numbers are an example, not from a trained model; they
are small so every step can be checked by hand.

**Part 1: RMSNorm against LayerNorm.** A token's vector is **(5, −1, 1, 3)**; the gains start at 1.

1. **Square each number:** 25, 1, 1, 9. Their sum is 36 and their mean is 36 / 4 = **9**.
2. **Root mean square:** √9 = **3**.
3. **Divide:** (5, −1, 1, 3) / 3 ≈ **(1.667, −0.333, 0.333, 1.000)**. That is RMSNorm.
4. **LayerNorm, for comparison:**
   - Mean (5 − 1 + 1 + 3) / 4 = 2; deviations (3, −3, −1, 1).
   - Variance (9 + 9 + 1 + 1) / 4 = 5; standard deviation √5 ≈ 2.236.
   - Result ≈ **(1.342, −1.342, −0.447, 0.447)**.
5. **The difference:** LayerNorm moves the mean to 0; RMSNorm keeps the mean and only fixes the
   size. The two agree exactly whenever the mean is already 0, as the RMSNorm paper notes.

**Part 2: a SwiGLU feed-forward layer.** The input is **x = (1, 2)**, with inner width 2.

1. **Two projections of the same input:**
   - xW with W = [[0, 1], [1, −1]]: (1·0 + 2·1, 1·1 + 2·(−1)) = **(2, −1)**.
   - xV with V = [[1, 0], [1, 0.5]]: (1·1 + 2·1, 1·0 + 2·0.5) = **(3, 1)**.
2. **Swish of the first:** z · σ(z).
   - σ(2) ≈ 0.8808, so Swish(2) ≈ 2 × 0.8808 = **1.7616**.
   - σ(−1) ≈ 0.2689, so Swish(−1) ≈ −1 × 0.2689 = **−0.2689**.
3. **Gate:** multiply number by number with xV: (1.7616 × 3, −0.2689 × 1) ≈ **(5.285, −0.269)**.
4. **Back to the model width** with W2 = [[1, 0], [1, 1]]:
   (5.285 + (−0.269), −0.269) ≈ **(5.016, −0.269)**.
5. **Against ReLU:** max(xW, 0) = (2, 0). ReLU sets −1 to exactly 0; Swish lets a small negative
   value (−0.269) through, and the second projection scales each number up or down.

**Part 3: rotary position embedding (the key equation).** One pair of numbers, rotated by
**θ = 90°** per position, chosen so every rotation is exact. Real models use θ₁ = 1 radian per
position for the first pair and smaller angles for the others.

Rotating (a, b) counter-clockwise by 90° gives (−b, a); by 180°, (−a, −b); by 270°, (b, −a).
The query is **q = (1, 0)** and the key is **k = (2, 1)**.

1. **Query at position 1, key at position 3** (distance 2):
   - q turned by 90° is (0, 1); k turned by 270° is (1, −2).
   - Score: 0 × 1 + 1 × (−2) = **−2**.
2. **Query at position 2, key at position 4** (distance 2 again):
   - q turned by 180° is (−1, 0); k turned by 360° is (2, 1).
   - Score: −1 × 2 + 0 × 1 = **−2**, the same as in step 1.
3. **Query at position 1, key at position 2** (distance 1):
   - q turned by 90° is (0, 1); k turned by 180° is (−2, −1).
   - Score: 0 × (−2) + 1 × (−1) = **−1**, a different value.
4. **Same position:** both turned by the same angle, so the score equals the plain dot product,
   1 × 2 + 0 × 1 = **2**.

**What this means:** RMSNorm only rescales each vector, SwiGLU gates one projection by another,
and RoPE makes an attention score depend on the distance between two tokens, not on where they
sit. None of the three changes the training objective: LLaMA still predicts the next token.
