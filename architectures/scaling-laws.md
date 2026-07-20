---
id: architecture-era/scaling-laws
type: architecture-era
slug: scaling-laws
schema_version: 1.0
title: "Neural Scaling Laws"
name: "Scaling Laws"
era: scaling
era_range: "2020–2022"
year: 2020
family: llm
core_innovation: "Loss falls as a predictable power law in parameters, data, and compute — enabling compute-optimal model sizing."
description: "The empirical power laws (Kaplan; Chinchilla) that turned model scaling from guesswork into budgeting."
key_features: ["Power-law loss vs. compute/params/data", "Predictable extrapolation", "Compute-optimal frontier", "~20 tokens per parameter (Chinchilla)"]
key_models: ["Kaplan et al. study (2020)", "Chinchilla 70B (2022)"]
paper_title: "Scaling Laws for Neural Language Models"
paper_url: "https://arxiv.org/abs/2001.08361"
influenced_by: [architecture-era/gpt]
led_to: []

eli5: "If you plot how good a language model gets against how big it is, you get a shockingly smooth line. That means you can measure a few small models and predict how good a giant one will be before spending millions to build it. Scaling stopped being a guess and became arithmetic."

analogy:
  title: "A recipe that scales predictably"
  body: "Imagine baking where doubling the ingredients reliably makes the cake a fixed amount better — and the improvement follows a known curve, so from two small test bakes you can predict how a huge cake will taste. Scaling laws are that curve for language models. Chinchilla then found the recipe was unbalanced: earlier giant models were all 'flour' (parameters) and too little 'water' (training data). Rebalance to roughly 20 parts data per part model, and a smaller cake beats a bigger, drier one made with the same oven time."

narrative:
  motivation: "GPT had shown bigger models are better, but that left the expensive question unanswered: how much better, and given a fixed compute budget, should you spend it on a bigger model or more training data? Teams were sizing multi-million-dollar runs on intuition."
  approach: "Kaplan et al. (2020) trained many language models across orders of magnitude in size, data, and compute, and simply plotted the test loss against each. Rather than a messy scatter, the points fell on clean straight lines on log-log axes — the signature of a power law."
  solution: "Test loss decreases as a smooth power law in model size, dataset size, and compute, with stable exponents. Because the curve is predictable, you can fit it on cheap small runs and extrapolate the loss of a far larger model before building it. Hoffmann et al. (2022, 'Chinchilla') then corrected the recipe: earlier large models (including GPT-3) were badly undertrained on too little data."
  outcome: "For a fixed compute budget, parameters and training tokens should scale together — roughly 20 tokens per parameter — so a smaller model trained on far more data (Chinchilla-70B) beat much larger ones. This reframed scaling from folklore into a budgeting discipline: given a FLOP budget, solve for the optimal (model size, token count) pair. Every serious pretraining run since is planned around these curves."

build_steps:
  - { title: "1. Pick an axis to scale", detail: "Choose what to vary — number of parameters N, dataset size D (tokens), or total compute C — holding the others generous so the chosen axis is the bottleneck." }
  - { title: "2. Train a ladder of models", detail: "Train many models spanning orders of magnitude on that axis and record each one's converged test loss (cross-entropy per token)." }
  - { title: "3. Plot loss on log-log axes", detail: "Plot loss against the scaled quantity with both axes logarithmic. A power law shows up as a straight line — that straight line is the whole finding." }
  - { title: "4. Fit the power law", detail: "Read off the slope (the exponent) and intercept. This gives an equation like L(N) = (Nc / N)^alpha that summarizes the trend." }
  - { title: "5. Extrapolate before you build", detail: "Use the fitted curve to forecast the loss of a much larger run, turning a blind bet into a budgeted prediction." }
  - { title: "6. Solve for compute-optimal", detail: "Given a fixed compute budget C, use the joint law to pick the best (N, D) pair — Chinchilla's answer is to grow parameters and tokens together, ~20 tokens per parameter." }

key_equation:
  latex: "L(N)=\\left(\\frac{N_c}{N}\\right)^{\\alpha_N}"
  plain: "Model loss falls as a power of the number of parameters: make the model N times bigger and the loss shrinks by a fixed fractional rate set by the exponent. On a log-log plot this is a straight line, which is why you can fit it on small models and extrapolate to large ones. Parallel laws hold for data size and for compute."
  terms:
    - { symbol: "L(N)", meaning: "the model's test loss (cross-entropy per token) as a function of size" }
    - { symbol: "N", meaning: "number of non-embedding model parameters — the scale being varied" }
    - { symbol: "N_c", meaning: "a fitted constant (a characteristic scale) that positions the curve" }
    - { symbol: "\\alpha_N", meaning: "the power-law exponent — the slope on log-log axes; larger means faster gains from scaling" }

strengths:
  - "Turns scaling into a prediction: fit on cheap small runs, forecast a giant model's loss before committing budget."
  - "Compute-optimal sizing — given a FLOP budget, solve for the best (parameters, tokens) split instead of guessing."
  - "Remarkably robust — the power-law form holds across many orders of magnitude and across modalities."
  - "De-risks huge investments by making the loss curve a plannable line, not a gamble."

weaknesses:
  - "Power laws describe pretraining loss, not downstream usefulness — lower loss doesn't guarantee a better product."
  - "Exponents are empirical fits, not derived from theory; they can shift with architecture, data quality, or objective."
  - "The extrapolation eventually breaks — data runs out, and irreducible-error floors bend the curve."
  - "Emergent capabilities and safety behaviors aren't captured by a smooth loss law, so it under-predicts qualitative jumps."

diagram: |
  flowchart TD
    LADDER["Train a ladder of models (vary N, D, C)"] --> MEASURE["Measure converged test loss"]
    MEASURE --> PLOT["Plot loss on log-log axes"]
    PLOT --> FIT["Straight line -> fit power law"]
    FIT --> EXTRAP["Extrapolate loss of a bigger run"]
    FIT --> OPT["Solve for compute-optimal (N, D)"]
    OPT --> CHIN["Chinchilla rule: ~20 tokens per parameter"]
diagram_caption: "Scaling laws in one loop: train a range of models, plot loss log-log, fit the straight-line power law, then extrapolate and pick the compute-optimal size."

notable_models:
  - { name: "Kaplan et al. scaling study", year: 2020, note: "The original — showed test loss follows clean power laws in parameters, data, and compute, enabling extrapolation." }
  - { name: "GPT-3 175B", year: 2020, note: "Scaled aggressively on parameters and, in hindsight, was undertrained on data — the case Chinchilla later corrected." }
  - { name: "Chinchilla 70B", year: 2022, note: "Proved compute-optimal balance (~20 tokens/param): a smaller, data-richer model beat much larger, data-starved ones." }
  - { name: "PaLM 540B", year: 2022, note: "A very large pre-Chinchilla model whose results retrospectively highlighted how much data undertraining left on the table." }
  - { name: "LLaMA", year: 2023, note: "Applied Chinchilla-style, data-heavy training to smaller models, showing compute-optimal recipes yield efficient open models." }

resources:
  - { kind: paper, title: "Scaling Laws for Neural Language Models (Kaplan et al., 2020)", url: "https://arxiv.org/abs/2001.08361" }
  - { kind: paper, title: "Training Compute-Optimal Large Language Models (Chinchilla; Hoffmann et al., 2022)", url: "https://arxiv.org/abs/2203.15556" }
  - { kind: blog, title: "chinchilla's wild implications (LessWrong)", url: "https://www.lesswrong.com/posts/6Fpvch8RR29qLEWNH/chinchilla-s-wild-implications" }
  - { kind: video, title: "Scaling laws and Chinchilla, from the 'Let's reproduce GPT-2' lecture (Andrej Karpathy)", url: "https://www.youtube.com/watch?v=l8pRSuU81PU" }

learn_first:
  - { label: "GPT & why scale matters", href: "/models/architecture/gpt", tag: "Architecture" }
  - { label: "Cross-entropy loss (what 'loss' measures)", href: "/learn/concepts/cross-entropy", tag: "Math" }
  - { label: "Power laws & log-log plots", href: "/learn/concepts/power-law", tag: "Math" }
  - { label: "Training compute (FLOPs) budgeting", href: "/learn/concepts/compute-flops", tag: "Hardware" }

tags: [scaling-laws, compute-optimal, chinchilla, llm]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2001.08361", title: "Scaling Laws for Neural Language Models (Kaplan et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2203.15556", title: "Training Compute-Optimal Large Language Models (Chinchilla; Hoffmann et al., 2022)", trust: 0.95 }
  - { url: "https://www.lesswrong.com/posts/6Fpvch8RR29qLEWNH/chinchilla-s-wild-implications", title: "chinchilla's wild implications (nostalgebraist)", trust: 0.75 }
---

## What Changed
Once GPT showed that bigger models were better, the open question was *how much* better, and where to
spend a fixed compute budget. Scaling laws answered it empirically. Kaplan et al. (2020) found that
test loss falls as a smooth **power law** in model size, dataset size, and compute over many orders of
magnitude — meaning you can train small models and *extrapolate* the loss of a much larger one before
building it.

Hoffmann et al. (2022, "Chinchilla") then corrected the recipe: earlier large models (including
GPT-3) were badly *undertrained* on too little data. For a compute budget, parameters and training
tokens should scale together — roughly **20 tokens per parameter** — so a smaller model trained on far
more data (Chinchilla-70B) beat much larger ones.

This reframed scaling from folklore into a budgeting discipline: given FLOPs, solve for the optimal
(model size, token count) pair. Every serious pretraining run since is planned around these curves.

## Key Ideas
- **Power-law loss** — $L$ decreases predictably as a power of parameters $N$, data $D$, and compute
  $C$; the exponents are stable enough to extrapolate.
- **Predictable extrapolation** — fit the curve on cheap small runs, forecast the loss of an expensive
  large run, and de-risk the budget before committing.
- **Compute-optimal frontier** — for fixed compute there is a best (size, tokens) trade-off; too big a
  model on too few tokens wastes compute.
- **Chinchilla correction** — ~20 tokens/parameter; prior frontier models were oversized and
  data-starved, so a smaller-but-longer-trained model won at equal compute.
