---
id: architecture-era/scaling-laws
type: architecture-era
slug: scaling-laws
schema_version: 2.5
title: "Neural Scaling Laws"
name: "Scaling Laws"
era: scaling
era_range: "2020–2022"
year: 2020
family: llm
core_innovation: "Loss falls as a predictable power law in parameters, data, and compute — enabling compute-optimal model sizing."
description: "Measured rules that predict how a language model's loss falls as its size, its training data and its training compute grow, used to decide how big a model to train, and on how much text, for a given budget."
key_features: ["Power-law loss vs. compute/params/data", "Predictable extrapolation", "Compute-optimal allocation of a fixed budget", "Model size and training tokens scaled equally (Chinchilla)"]
key_models: ["Kaplan et al. study (2020)", "Chinchilla 70B (2022)"]
paper_title: "Scaling Laws for Neural Language Models"
paper_url: "https://arxiv.org/abs/2001.08361"
influenced_by: [architecture-era/gpt]
led_to: []

analogy:
  title: "A fixed budget of teaching hours"
  body: "This is an analogy, not the mechanism. A school has a fixed budget of teaching hours and two ways to spend it: a bigger class of tutors for each lesson (model size) or more lessons (training text). A bigger class costs more hours per lesson, so the two choices compete for the same budget. Scaling laws are the measured curves of exam result against each choice. Chinchilla's finding, in these terms: earlier schools had spent almost everything on bigger classes and too little on lessons, and splitting the budget evenly scored better. Unlike an exam, the score here is one number, the model's loss on text it has not seen; the exact rule is the key equation below."

narrative:
  motivation: "**Bigger language models did better, but nobody could say how much better, or how to spend a budget.**\n\n- **The open question:** with a fixed amount of training compute, should it buy a **bigger model** or **more training text**?\n- **Why it matters:** **Hoffmann et al. (2022)** note that large models can typically be trained **only once**, so the size and the amount of text must be chosen before training starts.\n- **What was known:** the authors of both papers studied the **loss**, a single number for how well a model predicts held-out text."
  approach: "**Train many models and fit curves to their losses.**\n\n- **Kaplan et al. (2020), OpenAI:** decoder-only Transformers from **768** to **1.5 billion** non-embedding parameters, on **22 million** to **23 billion** tokens of the WebText2 dataset.\n- **Hoffmann et al. (2022), DeepMind:** **over 400** models from **70 million** to **over 16 billion** parameters, on **5 to 500 billion** tokens, each with a learning-rate schedule matched to its number of training tokens.\n- **The fit:** both papers fit **power laws**, which show up as straight lines when both axes are logarithmic."
  solution: "**Loss follows a power law in each of model size, data and compute.**\n\n- **Kaplan's three laws:** loss falls with model size $N$ (exponent **0.076**), with data $D$ (exponent **0.095**) and with compute (exponent **0.050**), each when the other two factors are not the bottleneck.\n- **Kaplan's allocation:** compute-efficient training uses **very large models** on a relatively modest amount of data and **stops well before convergence**; the optimal size grows as about $C^{0.73}$.\n- **Chinchilla's allocation:** all three of Hoffmann's methods find that model size and training tokens should grow in **equal proportion** (exponents near **0.5** each): for every doubling of model size, double the training tokens."
  outcome: "**Chinchilla tested the prediction at Gopher's budget.**\n\n- **The prediction:** for the compute used to train **Gopher**, an optimal model would be **4 times smaller** and trained on **4 times more** tokens.\n- **Chinchilla:** **70B** parameters on **1.4 trillion** tokens, the same number of FLOPs (floating-point operations) as Gopher, which has **280B** parameters trained on **300B** tokens.\n- **Results the authors report:** Chinchilla outperforms **Gopher (280B), GPT-3 (175B), Jurassic-1 (178B)** and **Megatron-Turing NLG (530B)** on a large range of downstream tasks; on MMLU its average 5-shot accuracy is **67.6%** against **60.0%** for Gopher.\n- **What followed:** the LLaMA authors (2023) trained **7B** to **65B** models on **1.0 to 1.4 trillion** tokens, aiming at the best performance for a given **inference** budget rather than a training budget."

key_equation:
  latex: "L(N,D)=E+\\frac{A}{N^{\\alpha}}+\\frac{B}{D^{\\beta}}"
  plain: "In words: the loss is a floor that no model can go below (E), plus a part that shrinks as the model gets bigger, plus a part that shrinks as it trains on more tokens. This is the form Hoffmann et al. fit to their runs (their Approach 3), with E = 1.69, A = 406.4, B = 410.7, α = 0.34 and β = 0.28. For a fixed budget, making the model bigger shrinks the second part but leaves fewer tokens, which grows the third; the worked example evaluates it for Gopher and Chinchilla."
  terms:
    - { symbol: "L(N,D)", meaning: "the predicted final loss, in nats per token, of a model with N parameters trained on D tokens" }
    - { symbol: "E", meaning: "the loss of an ideal model of the text; the authors say it should correspond to the entropy of natural text. Fitted value 1.69" }
    - { symbol: "A/N^{\\alpha}", meaning: "how far a perfectly trained model of N parameters falls short of the ideal. A = 406.4, α = 0.34" }
    - { symbol: "B/D^{\\beta}", meaning: "the shortfall from training on only D tokens, a finite number of steps. B = 410.7, β = 0.28" }
    - { symbol: "N", meaning: "the number of model parameters" }
    - { symbol: "D", meaning: "the number of training tokens" }

strengths:
  - "**Predict before you train:** Kaplan et al. write that their relations go beyond observation to give a **predictive framework**, with some trends spanning **more than seven orders of magnitude**."
  - "**Shape matters little:** at a fixed number of non-embedding parameters, loss depends **very weakly** on depth, width and number of attention heads; a 6-layer model 4,288 wide comes within **3%** of the loss of the 48-layer, 1,600-wide GPT-2 model."
  - "**A budget rule:** Hoffmann et al. tabulate the compute-optimal tokens and FLOPs for each model size; for example **10B** parameters on **205.1B** tokens at **1.23 × 10²²** FLOPs (Table 3)."
  - "**Cheaper to use:** because Chinchilla is **4 times** smaller than Gopher, its memory footprint and inference cost are smaller, the authors note."

weaknesses:
  - "**Fitted to loss, not tasks:** the laws predict cross-entropy loss, not task scores; Hoffmann et al. report that Chinchilla, despite its lower loss, **underperforms** Gopher on **4** of the 57 MMLU tasks, among them college mathematics and formal logic."
  - "**The two papers disagree:** Kaplan's optimal model size grows as $C^{0.73}$ and tokens as $C^{0.27}$; Hoffmann's methods give about **0.5** for both. Hoffmann et al. attribute part of the gap to Kaplan's **fixed learning-rate schedule** and mostly small models, many under **100M** parameters."
  - "**Few large tests:** Hoffmann et al. had only **two** comparable large runs (Chinchilla and Gopher), saw **curvature** at high compute that may mean they still overestimate the optimal size, and trained every run on **less than one epoch** of data."
  - "**The lines must bend:** Kaplan et al. note that the trends must level off before zero loss, because natural language has non-zero entropy, and that their own laws contradict each other at around **10⁴ PF-days** of compute."
  - "**Training budget only:** the LLaMA authors point out that compute-optimal training ignores the **inference** budget; their **7B** model kept improving after **1T** tokens, where Hoffmann et al. recommend a **10B** model on **200B** tokens."

comparison:
  before: "Kaplan et al. (2020)"
  after: "Hoffmann et al. (2022, Chinchilla)"
  rows:
    - { aspect: "Models trained", before: "768 to 1.5 billion non-embedding parameters, on 22 million to 23 billion tokens", after: "Over 400 models, 70 million to over 16 billion parameters, on 5 to 500 billion tokens", source: "https://arxiv.org/abs/2203.15556" }
    - { aspect: "Learning-rate schedule", before: "The same number of training tokens and schedule for every model, as Hoffmann et al. describe it", after: "A cosine schedule whose length matches each run's number of training tokens", source: "https://arxiv.org/abs/2203.15556" }
    - { aspect: "How fast the optimal model size grows with compute (a in N ∝ Cᵃ)", before: "0.73", after: "0.50, 0.49 and 0.46 (three methods)", source: "https://arxiv.org/abs/2203.15556" }
    - { aspect: "How fast the optimal number of tokens grows (b in D ∝ Cᵇ)", before: "0.27", after: "0.50, 0.51 and 0.54 (three methods)", source: "https://arxiv.org/abs/2203.15556" }
    - { aspect: "Advice for 10 times more compute", before: "Model about 5.5 times bigger, tokens about 1.8 times more (Hoffmann et al.'s reading of Kaplan)", after: "Model size and tokens grown in equal proportion", source: "https://arxiv.org/abs/2203.15556" }

key_numbers:
  columns: ["Setting", "Gopher", "Chinchilla"]
  rows:
    - { cells: ["Parameters", "280 billion", "70 billion"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["Training tokens", "300 billion", "1.4 trillion"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["Tokens per parameter (tokens ÷ parameters)", "about 1.1", "20"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["Layers", "80", "80"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["Attention heads", "128", "64"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["Key and value size", "128", "128"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["Numbers in each token's vector (d_model)", "16,384", "8,192"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["Maximum learning rate", "4 × 10⁻⁵", "1 × 10⁻⁴"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["Batch size in tokens, doubled midway through training", "3M → 6M", "1.5M → 3M"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["Optimiser", "Adam", "AdamW"], source: "https://arxiv.org/abs/2203.15556" }
    - { cells: ["MMLU, average 5-shot accuracy", "60.0%", "67.6%"], source: "https://arxiv.org/abs/2203.15556" }

misunderstandings:
  - misunderstanding: "Scaling laws say a bigger model is always the better choice."
    truth: "Only when compute is unlimited. For a **fixed** budget there is an optimal size: Hoffmann et al. find a clear **valley** in loss for each budget (their IsoFLOP curves), and predict that for Gopher's budget the optimal model is **4 times smaller** and trained on **4 times more** tokens. Their Chinchilla, at 70B parameters, outperformed the 280B Gopher on the same compute."
    source: "https://arxiv.org/abs/2203.15556"
  - misunderstanding: "Chinchilla established a fixed rule of 20 tokens per parameter."
    truth: "The paper's rule is that parameters and tokens should grow in **equal proportion**; it does not state a fixed ratio. Its Table 3 projections work out to about **20** tokens per parameter (1B parameters on **20.2B** tokens; 67B on **1.5T**, about 22), and they answer one question only: the lowest loss for a given **training** budget."
    source: "https://arxiv.org/abs/2203.15556"
  - misunderstanding: "A lower loss from the scaling law means a better score on every task."
    truth: "The laws predict **cross-entropy loss**, the average surprise of the model at the next token. Kaplan et al. write that it will be important to investigate whether continued improvement in loss turns into improvement on **relevant language tasks**, and that smooth gains in loss may hide **qualitative** changes in what a model can do."
    source: "https://arxiv.org/abs/2001.08361"
  - misunderstanding: "The fitted constants are universal constants of language."
    truth: "Kaplan et al. state that the values of $N_c$, $D_c$ and $C_c^{min}$ depend on the **vocabulary size and tokenization** and so have **no fundamental meaning**; their numbers are tied to the WebText2 training set. Changing the tokenizer rescales them."
    source: "https://arxiv.org/abs/2001.08361"

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
  - { name: "Kaplan et al. scaling study", year: 2020, note: "The original: test loss follows power laws in parameters, data and compute, enabling extrapolation." }
  - { name: "GPT-3 175B", year: 2020, note: "Trained on 300 billion tokens; one of the large models Chinchilla outperformed with 70B parameters, in Hoffmann et al.'s evaluation." }
  - { name: "Chinchilla 70B", year: 2022, note: "A predicted compute-optimal model: same compute as Gopher (280B), 70B parameters on 1.4 trillion tokens, and better on a large range of downstream tasks." }
  - { name: "LLaMA", year: 2023, note: "7B–65B models trained on 1.0–1.4 trillion tokens; LLaMA-13B outperforms GPT-3 (175B) on most benchmarks, per the authors." }

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
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/2001.08361", title: "Scaling Laws for Neural Language Models (Kaplan et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2203.15556", title: "Training Compute-Optimal Large Language Models (Chinchilla; Hoffmann et al., 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2302.13971", title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", trust: 0.95 }
---

## What Changed

Both papers fit the same kind of law; they disagree on **how to split a budget**. Kaplan et al.
(2020) advise spending most extra compute on a bigger model; Hoffmann et al. (2022) advise
growing the model and the training text **equally**.

How to read the table above:

- **N** is the number of parameters (the learned numbers of the model) and **D** the number of
  training tokens; **C** is the training compute.
- **N ∝ Cᵃ** reads "N grows as C to the power a". With a = 0.5, 100 times more compute gives a
  model 10 times bigger; with a = 0.73 it gives one about 29 times bigger.
- **Three methods:** Hoffmann et al. estimated the exponents three independent ways and got
  nearly the same answer each time.
- **Why they differ:** Hoffmann et al. point to Kaplan's single learning-rate schedule, which
  overstates the loss of short runs, and to Kaplan's mostly smaller models.
- **The test:** Chinchilla (70B parameters, 1.4T tokens) against Gopher (280B, 300B tokens), on
  the same compute.

## Key Ideas

- **Power-law loss:** loss falls as a power of parameters, data and compute; on log-log axes each
  law is a straight line.
- **Predict, then train:** fit the line on small runs and read off the loss of a larger one.
- **A best split for every budget:** at fixed compute, a model can be too big as well as too small.
- **Chinchilla's revision:** grow parameters and tokens in equal proportion.

## How It Works

The figure above has **three panels**, one per scale factor. Both axes are logarithmic: each tick
is ten times the one before, which turns a power law into a straight line.

A **token** is a piece of text, a word or part of a word. The **loss** is the cross-entropy: the
average of −ln(probability the model gave the correct next token), in **nats** per token. Lower
is better.

1. **Measure the loss.** Kaplan et al. average it over a **1,024**-token context on held-out text
   of WebText2, a web-text dataset of **96 GB**.
2. **Count the model size N.** They count parameters **excluding** the embedding tables, about
   $12 \cdot n_{layer} \cdot d_{model}^2$; counting embeddings gave much less clean trends.
3. **Count the data D,** in tokens.
4. **Count the compute C.** Each training token costs about **6N** floating-point operations
   (about 2N forward, twice that backward), so $C \approx 6ND$. They quote C in **PF-days**:
   $10^{15} \times 24 \times 3600 = 8.64 \times 10^{19}$ operations.
5. **Left panel: model size.** Models trained close to convergence on plenty of data follow
   $L(N) = (N_c/N)^{\alpha_N}$ with $\alpha_N \approx 0.076$ and $N_c \approx 8.8 \times 10^{13}$. The
   slope of the line is $-\alpha_N$.
6. **Middle panel: data.** A 36-layer model 1,280 wide, stopped early on subsets of the data,
   follows $L(D) = (D_c/D)^{\alpha_D}$ with $\alpha_D \approx 0.095$ and $D_c \approx 5.4 \times 10^{13}$
   tokens.
7. **Right panel: compute.** Taking the best model size for each budget gives an exponent of
   **0.050**. Each of the three laws holds only when the other two factors are **not the
   bottleneck**.
8. **Size and data together.** Kaplan et al. combine the first two laws; overfitting depends on
   $N^{0.74}/D$, so a model **8 times** bigger needs only about **5 times** more data.
9. **Split a budget, Kaplan's way.** The optimal model size grows as about $C^{0.73}$: most extra
   compute goes into a bigger model, trained short of convergence.
10. **Split a budget, Hoffmann's way.** With each run's schedule matched to its length, three
    methods:
    - **Fix sizes, vary tokens:** for every FLOP count, keep the run with the lowest loss.
    - **IsoFLOP profiles:** for **9** budgets from $6 \times 10^{18}$ to $3 \times 10^{21}$ FLOPs, vary
      the model size and find the bottom of the valley in loss.
    - **Fit a formula:** fit $L(N,D) = E + A/N^{\alpha} + B/D^{\beta}$ to every run, then minimise
      it under $C \approx 6ND$.
11. **The answer.** All three give exponents near **0.5** for both size and tokens: grow them
    together.

## Worked Example

**The papers' fitted formulas, evaluated with their own published constants.** The results are
what the fits predict, not losses measured on a trained model. Each power is worked through a
base-10 logarithm so it can be checked with any calculator.

**Part 1: Kaplan's law for model size**, $L(N) = (N_c/N)^{0.076}$ with $N_c = 8.8 \times 10^{13}$.

1. **A model of 1 billion parameters:** $N_c/N = 8.8 \times 10^{13} / 10^9 = 88{,}000$.
2. **Take the logarithm:** $\log_{10} 88{,}000 \approx 4.9445$.
3. **Multiply by the exponent:** $0.076 \times 4.9445 \approx 0.3758$.
4. **Undo the logarithm:** $10^{0.3758} \approx$ **2.376** nats per token.
5. **Double the model to 2 billion:** the ratio is 44,000; $\log_{10} 44{,}000 \approx 4.6435$;
   $0.076 \times 4.6435 \approx 0.3529$; $10^{0.3529} \approx$ **2.254**.
6. **Check:** $2.254 / 2.376 \approx 0.949 = 2^{-0.076}$. Every doubling of N multiplies the loss
   by this same factor, which Kaplan et al. round to **0.95**.

**Part 2: Chinchilla's formula, Gopher against Chinchilla**, with $E = 1.69$, $A = 406.4$,
$B = 410.7$, $\alpha = 0.34$ and $\beta = 0.28$.

1. **Gopher's model term** ($N = 2.8 \times 10^{11}$): $N^{0.34} = 10^{0.34 \times 11.4472} = 10^{3.8920} \approx 7{,}799$,
   and $406.4 / 7{,}799 \approx 0.0521$.
2. **Gopher's data term** ($D = 3 \times 10^{11}$): $D^{0.28} = 10^{0.28 \times 11.4771} = 10^{3.2136} \approx 1{,}635$,
   and $410.7 / 1{,}635 \approx 0.2511$.
3. **Gopher's predicted loss:** $1.69 + 0.0521 + 0.2511 \approx$ **1.993**.
4. **Chinchilla's model term** ($N = 7 \times 10^{10}$): $N^{0.34} = 10^{0.34 \times 10.8451} = 10^{3.6873} \approx 4{,}868$,
   and $406.4 / 4{,}868 \approx 0.0835$.
5. **Chinchilla's data term** ($D = 1.4 \times 10^{12}$): $D^{0.28} = 10^{0.28 \times 12.1461} = 10^{3.4009} \approx 2{,}517$,
   and $410.7 / 2{,}517 \approx 0.1632$.
6. **Chinchilla's predicted loss:** $1.69 + 0.0835 + 0.1632 \approx$ **1.937**.
7. **The compute, by the 6ND rule of thumb:**
   - Gopher: $6 \times 2.8 \times 10^{11} \times 3 \times 10^{11} = 5.04 \times 10^{23}$ FLOPs.
   - Chinchilla: $6 \times 7 \times 10^{10} \times 1.4 \times 10^{12} = 5.88 \times 10^{23}$ FLOPs.
   - The paper counts FLOPs in more detail (its Appendix F) and states that the two used the
     same number.

**Part 3: tokens per parameter.**

- **Chinchilla:** $1.4 \times 10^{12} / 7 \times 10^{10} = 20$.
- **Gopher:** $3 \times 10^{11} / 2.8 \times 10^{11} \approx 1.07$.
- **The paper's Table 3:** 1B parameters on 20.2B tokens (20.2 per parameter) and 10B on 205.1B
  (about 20.5).

**What this means:** on about the same compute, the formula gives the smaller model trained on
more text the lower loss. Its data term falls from **0.251** to **0.163**, more than its model term
rises (**0.052** to **0.083**), while the floor $E = 1.69$ is the same for both. The two papers fit
different datasets and tokenizers, so the losses of Part 1 and Part 2 cannot be compared with
each other.
