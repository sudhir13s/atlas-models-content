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

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 428" role="img" aria-label="Schematic of Kaplan et al.'s scaling laws: three log-log plots in which test loss falls along a straight line as model parameters N, dataset size D and compute C grow, each a power law with a small fitted exponent (0.076, 0.095 and 0.050).">
  <text class="d-title" x="430" y="22" text-anchor="middle">Test loss falls as a power law in each of model size, data and compute</text>
  <text class="d-note" x="430" y="38" text-anchor="middle">same loss scale in all three panels; each law holds when the other two factors are not the bottleneck</text>
  <text class="d-label" x="0" y="0" text-anchor="middle" transform="translate(24 192) rotate(-90)">test loss (log scale)</text>
  <text class="d-title" x="182" y="60" text-anchor="middle">Model size</text>
  <text class="d-math" x="182" y="78" text-anchor="middle">L(N) = (N<tspan class="d-note" dy="3">c</tspan><tspan dy="-3">​</tspan> / N)<tspan class="d-note" dy="-5">α</tspan><tspan class="d-note" dy="3">N</tspan><tspan dy="2">,  α</tspan><tspan class="d-note" dy="3">N</tspan><tspan dy="-3">​</tspan> ≈ 0.076</text>
  <path class="d-line" d="M84 277.8733387597771 H280" opacity="0.35"/>
  <path class="d-line-strong" d="M80 277.8733387597771 H84"/>
  <text class="d-note" x="76" y="281.3733387597771" text-anchor="end">2</text>
  <path class="d-line" d="M84 223.50887223062222 H280" opacity="0.35"/>
  <path class="d-line-strong" d="M80 223.50887223062222 H84"/>
  <text class="d-note" x="76" y="227.00887223062222" text-anchor="end">3</text>
  <path class="d-line" d="M84 184.93666937988854 H280" opacity="0.35"/>
  <path class="d-line-strong" d="M80 184.93666937988854 H84"/>
  <text class="d-note" x="76" y="188.43666937988854" text-anchor="end">4</text>
  <path class="d-line" d="M84 130.57220285073362 H280" opacity="0.35"/>
  <path class="d-line-strong" d="M80 130.57220285073362 H84"/>
  <text class="d-note" x="76" y="134.07220285073362" text-anchor="end">6</text>
  <path class="d-line" d="M84 92 H280" opacity="0.35"/>
  <path class="d-line-strong" d="M80 92 H84"/>
  <text class="d-note" x="76" y="95.5" text-anchor="end">8</text>
  <path class="d-line" d="M84 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M84 292 V296"/>
  <text class="d-note" x="84" y="309" text-anchor="middle">10³</text>
  <path class="d-line" d="M112 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M112 292 V296"/>
  <path class="d-line" d="M140 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M140 292 V296"/>
  <text class="d-note" x="140" y="309" text-anchor="middle">10⁵</text>
  <path class="d-line" d="M168 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M168 292 V296"/>
  <path class="d-line" d="M196 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M196 292 V296"/>
  <text class="d-note" x="196" y="309" text-anchor="middle">10⁷</text>
  <path class="d-line" d="M224 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M224 292 V296"/>
  <path class="d-line" d="M252 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M252 292 V296"/>
  <text class="d-note" x="252" y="309" text-anchor="middle">10⁹</text>
  <path class="d-line" d="M280 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M280 292 V296"/>
  <path class="d-line-strong" d="M84 92 V292 H280"/>
  <text class="d-label" x="182" y="328" text-anchor="middle">parameters N, non-embedding (log)</text>
  <path class="d-line-accent" d="M84.0 114.0 L252.0 254.8"/>
  <path class="d-line-accent" d="M252.0 254.8 L280.0 278.3" stroke-dasharray="4 4" opacity="0.7"/>
  <path class="d-line" d="M126.0 149.2 V198.5 H184.8" stroke-dasharray="3 3"/>
  <text class="d-math" x="120" y="177.8469125395119" text-anchor="end">slope −α</text>
  <text class="d-title" x="460" y="60" text-anchor="middle">Dataset size</text>
  <text class="d-math" x="460" y="78" text-anchor="middle">L(D) = (D<tspan class="d-note" dy="3">c</tspan><tspan dy="-3">​</tspan> / D)<tspan class="d-note" dy="-5">α</tspan><tspan class="d-note" dy="3">D</tspan><tspan dy="2">,  α</tspan><tspan class="d-note" dy="3">D</tspan><tspan dy="-3">​</tspan> ≈ 0.095</text>
  <path class="d-line" d="M362 277.8733387597771 H558" opacity="0.35"/>
  <path class="d-line-strong" d="M358 277.8733387597771 H362"/>
  <path class="d-line" d="M362 223.50887223062222 H558" opacity="0.35"/>
  <path class="d-line-strong" d="M358 223.50887223062222 H362"/>
  <path class="d-line" d="M362 184.93666937988854 H558" opacity="0.35"/>
  <path class="d-line-strong" d="M358 184.93666937988854 H362"/>
  <path class="d-line" d="M362 130.57220285073362 H558" opacity="0.35"/>
  <path class="d-line-strong" d="M358 130.57220285073362 H362"/>
  <path class="d-line" d="M362 92 H558" opacity="0.35"/>
  <path class="d-line-strong" d="M358 92 H362"/>
  <path class="d-line" d="M362 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M362 292 V296"/>
  <text class="d-note" x="362" y="309" text-anchor="middle">10⁷</text>
  <path class="d-line" d="M411 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M411 292 V296"/>
  <text class="d-note" x="411" y="309" text-anchor="middle">10⁸</text>
  <path class="d-line" d="M460 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M460 292 V296"/>
  <text class="d-note" x="460" y="309" text-anchor="middle">10⁹</text>
  <path class="d-line" d="M509 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M509 292 V296"/>
  <text class="d-note" x="509" y="309" text-anchor="middle">10¹⁰</text>
  <path class="d-line" d="M558 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M558 292 V296"/>
  <text class="d-note" x="558" y="309" text-anchor="middle">10¹¹</text>
  <path class="d-line-strong" d="M362 92 V292 H558"/>
  <text class="d-label" x="460" y="328" text-anchor="middle">dataset size D, tokens (log)</text>
  <path class="d-line-accent" d="M411.0 202.7 L509.0 261.3"/>
  <path class="d-line-accent" d="M509.0 261.3 L558.0 290.7" stroke-dasharray="4 4" opacity="0.7"/>
  <path class="d-line" d="M435.5 217.3 V237.9 H469.8" stroke-dasharray="3 3"/>
  <text class="d-math" x="429.5" y="231.6130675967996" text-anchor="end">slope −α</text>
  <text class="d-title" x="738" y="60" text-anchor="middle">Compute</text>
  <text class="d-math" x="738" y="78" text-anchor="middle">L(C) = (C<tspan class="d-note" dy="3">c</tspan><tspan dy="-3">​</tspan> / C)<tspan class="d-note" dy="-5">α</tspan><tspan class="d-note" dy="3">C</tspan><tspan dy="2">,  α</tspan><tspan class="d-note" dy="3">C</tspan><tspan dy="-3">​</tspan> ≈ 0.050</text>
  <path class="d-line" d="M640 277.8733387597771 H836" opacity="0.35"/>
  <path class="d-line-strong" d="M636 277.8733387597771 H640"/>
  <path class="d-line" d="M640 223.50887223062222 H836" opacity="0.35"/>
  <path class="d-line-strong" d="M636 223.50887223062222 H640"/>
  <path class="d-line" d="M640 184.93666937988854 H836" opacity="0.35"/>
  <path class="d-line-strong" d="M636 184.93666937988854 H640"/>
  <path class="d-line" d="M640 130.57220285073362 H836" opacity="0.35"/>
  <path class="d-line-strong" d="M636 130.57220285073362 H640"/>
  <path class="d-line" d="M640 92 H836" opacity="0.35"/>
  <path class="d-line-strong" d="M636 92 H640"/>
  <path class="d-line" d="M640 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M640 292 V296"/>
  <text class="d-note" x="640" y="309" text-anchor="middle">10⁻⁸</text>
  <path class="d-line" d="M659.6 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M659.6 292 V296"/>
  <path class="d-line" d="M679.2 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M679.2 292 V296"/>
  <text class="d-note" x="679.2" y="309" text-anchor="middle">10⁻⁶</text>
  <path class="d-line" d="M698.8 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M698.8 292 V296"/>
  <path class="d-line" d="M718.4 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M718.4 292 V296"/>
  <text class="d-note" x="718.4" y="309" text-anchor="middle">10⁻⁴</text>
  <path class="d-line" d="M738 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M738 292 V296"/>
  <path class="d-line" d="M757.6 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M757.6 292 V296"/>
  <text class="d-note" x="757.6" y="309" text-anchor="middle">10⁻²</text>
  <path class="d-line" d="M777.2 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M777.2 292 V296"/>
  <path class="d-line" d="M796.8 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M796.8 292 V296"/>
  <text class="d-note" x="796.8" y="309" text-anchor="middle">10⁰</text>
  <path class="d-line" d="M816.4 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M816.4 292 V296"/>
  <path class="d-line" d="M836 92 V292" opacity="0.35"/>
  <path class="d-line-strong" d="M836 292 V296"/>
  <text class="d-note" x="836" y="309" text-anchor="middle">10²</text>
  <path class="d-line-strong" d="M640 92 V292 H836"/>
  <text class="d-label" x="738" y="328" text-anchor="middle">compute C, PF-days (log)</text>
  <path class="d-line-accent" d="M640.0 116.2 L796.8 239.7"/>
  <path class="d-line-accent" d="M796.8 239.7 L836.0 270.6" stroke-dasharray="4 4" opacity="0.7"/>
  <path class="d-line" d="M679.2 147.1 V190.3 H734.1" stroke-dasharray="3 3"/>
  <text class="d-math" x="673.2" y="172.72590173877148" text-anchor="end">slope −α</text>
  <path class="d-line" d="M16 358 H844" opacity="0.6"/>
  <text class="d-label" x="430" y="378" text-anchor="middle">A straight line on log–log axes is a power law, L ∝ N⁻ᵅ: doubling N multiplies the loss by 2⁻⁰·⁰⁷⁶ ≈ 0.95, every time.</text>
  <text class="d-label" x="430" y="396" text-anchor="middle">So a bigger model, more data and more compute, grown together, give predictable gains that can be forecast from small runs.</text>
  <text class="d-note" x="430" y="416" text-anchor="middle">Lines drawn from the paper's fitted laws (WebText2; C is the compute-efficient C<tspan class="d-note" dy="3">min</tspan><tspan dy="-3">​</tspan>); solid = fitted span, dashed = extrapolation; data points omitted.</text>
  </svg>
diagram_caption: "Schematic of Kaplan et al. (2020): on log–log axes, test loss falls along a straight power-law line as parameters N, data D and compute C grow (exponents 0.076, 0.095, 0.050), so gains from scale are predictable."
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
