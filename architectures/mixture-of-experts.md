---
id: architecture-era/mixture-of-experts
type: architecture-era
slug: mixture-of-experts
schema_version: 2.5
title: "Mixture of Experts (Sparse MoE)"
name: "Mixture of Experts"
era: post-transformer
era_range: "2017–2026"
year: 2017
family: llm
core_innovation: "Replace a dense feed-forward block with many expert feed-forward networks and a learned router that activates only a few per token, so total parameters grow without a matching growth in computation per token."
description: "A layer that holds many separate feed-forward networks, called experts, and sends each token through only a few of them, chosen by a small learned router."
key_features: ["Several expert feed-forward networks per MoE layer", "A learned router selects a few experts per token", "Sparse activation", "Total parameters far exceed active parameters"]
key_models: ["Switch Transformer (2021)", "Mixtral 8x7B (2023)", "DeepSeek-V3 (2024)"]
paper_title: "Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer"
paper_url: "https://arxiv.org/abs/1701.06538"
influenced_by: [architecture-era/transformer]
led_to: [architecture-era/reasoning-models]

analogy:
  title: "A hospital with specialists and a triage nurse"
  body: "This is an analogy, not the mechanism. A dense model is one doctor who sees every patient. A mixture-of-experts layer is a hospital: many specialists sit ready, and a triage nurse at the door sends each patient to one or two of them. The hospital holds far more expertise than one doctor, but each patient occupies only a few specialists. The router is the nurse, the experts are the specialists, and k is how many specialists each patient sees. Unlike hospital specialists, the experts of a trained model are not assigned topics; see Common misunderstandings."

narrative:
  motivation: "In a dense layer every parameter takes part in computing every token, so more parameters means more computation for every token. Shazeer et al. (2017) report greater than 1000x improvements in model capacity with only minor losses in computational efficiency by making that computation sparse."
  approach: "Jacobs et al. (1991) trained systems of many separate networks, each of which learns to handle a subset of the training cases. Shazeer et al. (2017) built a layer of up to thousands of feed-forward sub-networks with a trainable gating network that keeps only the top k scores for each example, applied between stacked LSTM layers. GShard (2020) used a variant of top-2 gating in a Transformer encoder and decoder, scaling beyond 600 billion parameters."
  solution: "The router is a learned linear layer that scores every expert for a token. Mixtral keeps the top two scores and applies a softmax over them; the layer's output is the weighted sum of those experts' outputs. Switch Transformer routes each token to a single expert and adds an auxiliary load-balancing loss; DeepSeek-V3 uses an auxiliary-loss-free balancing strategy."
  outcome: "Mixtral 8x7B has 47B parameters and uses 13B active parameters per token; its authors report that it outperforms or matches Llama 2 70B and GPT-3.5 across all evaluated benchmarks. DeepSeek-V3 has 671B total parameters with 37B activated for each token, and its report states 2.788M H800 GPU hours for its full training."

key_equation:
  latex: "y = \\sum_{i \\in \\mathrm{TopK}(x)} g_i(x)\\, E_i(x)"
  plain: "In words: the layer's output is a weighted sum over only the k experts the router picked. Each chosen expert Eᵢ processes the token, the router weight gᵢ sets its share, and experts outside the top k contribute nothing and are not computed. The worked example above evaluates this sum for four experts and k = 2."
  terms:
    - { symbol: "x", meaning: "the token's vector arriving at the layer (4096 numbers in Mixtral)" }
    - { symbol: "E_i(x)", meaning: "the output of expert i, a feed-forward network of its own (SwiGLU in Mixtral)" }
    - { symbol: "g_i(x)", meaning: "the router's weight for expert i; 0 unless i is in the top k. In Mixtral, a softmax over the top-k scores" }
    - { symbol: "TopK(x)", meaning: "the k experts with the highest router scores for this token; k = 1 in Switch Transformer, 2 in Mixtral, 8 routed experts in DeepSeek-V3" }

strengths:
  - "Mixtral's authors: the parameter count can grow while the computational cost stays effectively constant, because only k experts run for each token."
  - "Switch Transformer's authors report up to 7x increases in pre-training speed with the same computational resources."
  - "GLaM's authors report 1.2 trillion parameters at about one third of the energy used to train GPT-3 and half of its computation FLOPs for inference."
  - "Only the feed-forward layer changes: Switch Transformer replaces the dense feed-forward layer of the Transformer and leaves attention as it is."

weaknesses:
  - "Memory follows the total parameter count: Mixtral's authors write that its serving memory is proportional to its sparse parameter count, 47B, not the 13B active."
  - "Mixtral's authors note that the routing mechanism adds overhead, and that spreading experts over devices introduces challenges in load balancing."
  - "Switch Transformer's authors write that widespread adoption has been hindered by complexity, communication costs and training instability."

visual:
  kind: distribution
  tokens: ["Expert 1", "Expert 2", "Expert 3", "Expert 4"]
  logits: [1.0, 2.5, 0.5, 2.0]
  truncate: { top_k: 2 }
  panel_titles: ["Softmax over all 4 router scores", "Top 2 kept (k = 2), rescaled to add up to 1"]
  caption: "The router weights for one token over four experts, computed from the example scores 1.0, 2.5, 0.5 and 2.0 of the worked example. Left: a softmax over all four scores. Right: only the two highest are kept and rescaled, which gives the same weights as Mixtral's softmax over the top-2 scores. The scores are an example, not from a trained model."

comparison:
  before: "Dense feed-forward layer"
  after: "Mixture-of-experts layer"
  rows:
    - { aspect: "Feed-forward networks in the layer", before: "One", after: "Several experts side by side (8 in each Mixtral layer)", source: "https://arxiv.org/abs/2401.04088" }
    - { aspect: "Which parameters a token passes through", before: "All of the layer's parameters", after: "Only the k experts the router picks (Mixtral: 13B of its 47B parameters)", source: "https://arxiv.org/abs/2401.04088" }
    - { aspect: "Computation per token as experts are added", before: "Grows with every added parameter", after: "Stays effectively constant, set by k", source: "https://arxiv.org/abs/2401.04088" }
    - { aspect: "Memory needed to serve the model", before: "Proportional to its parameters", after: "Still proportional to all parameters, active or not (Mixtral: 47B)", source: "https://arxiv.org/abs/2401.04088" }
    - { aspect: "Extra parts", before: "None", after: "A router, plus a way to keep tokens spread across experts (Switch Transformer: an auxiliary loss)", source: "https://arxiv.org/abs/2101.03961" }

key_numbers:
  columns: ["Model", "Experts per MoE layer", "Experts used per token", "Total parameters", "Parameters used per token"]
  rows:
    - { cells: ["Switch-C (2021)", "2,048", "1", "1,571 billion", "not given in the paper's Table 9"], source: "https://arxiv.org/abs/2101.03961" }
    - { cells: ["GLaM, largest model (2021)", "64", "2", "1.2 trillion", "96.6 billion (8%)"], source: "https://arxiv.org/abs/2112.06905" }
    - { cells: ["Mixtral 8x7B (2023)", "8", "2", "47 billion", "13 billion"], source: "https://arxiv.org/abs/2401.04088" }
    - { cells: ["DeepSeek-V3 (2024)", "256 routed + 1 shared", "8 routed + the shared one", "671 billion", "37 billion"], source: "https://arxiv.org/abs/2412.19437" }

misunderstandings:
  - misunderstanding: "Mixtral 8x7B has 8 × 7 = 56 billion parameters."
    truth: "Its paper gives **47B** parameters in total and **13B** used per token. Mixtral has the same architecture as Mistral 7B except that **each layer has 8 feed-forward blocks**; the attention layers are not copied eight times, so the total is less than 8 × 7B."
    source: "https://arxiv.org/abs/2401.04088"
  - misunderstanding: "Each expert specialises in a topic, such as maths, biology or code."
    truth: "Mixtral's authors looked at which experts tokens go to and **did not observe obvious patterns based on the topic**. They found routing more aligned with **syntax**: for example, the indentation tokens in code were always assigned to the same experts, and consecutive tokens were often sent to the same experts."
    source: "https://arxiv.org/abs/2401.04088"
  - misunderstanding: "A mixture-of-experts model needs memory only for its active parameters."
    truth: "All experts must be held in memory, because any token may pick any of them. Mixtral's authors write that its serving memory is **proportional to its sparse parameter count, 47B**; the 13B active parameters set the **computation** per token, not the memory."
    source: "https://arxiv.org/abs/2401.04088"
  - misunderstanding: "Mixture of experts was invented for Transformers."
    truth: "Jacobs et al. described **adaptive mixtures of local experts in 1991**, and Shazeer et al. (2017) applied their sparsely-gated layer **between stacked LSTM layers**, a recurrent model. GShard (2020) and Switch Transformer (2021) later placed such layers inside Transformers."
    source: "https://arxiv.org/abs/1701.06538"

diagram: |
  flowchart TD
    T["Token vector"] --> R["Router (gating network)"]
    R -->|"top-1 weight"| E2["Expert 2"]
    R -->|"top-2 weight"| E4["Expert 4"]
    R -. "skipped" .-> E1["Expert 1"]
    R -. "skipped" .-> E3["Expert 3"]
    E2 --> S["Weighted sum"]
    E4 --> S
    S --> OUT["Layer output"]
diagram_caption: "In every MoE layer of DeepSeek-V3 the router picks 8 of 256 routed experts for each token, and one shared expert runs for every token; the first three layers keep a dense feed-forward network (DeepSeek-V3 report, Section 4.2)."

notable_models:
  - { name: "Switch Transformer", year: 2021, note: "Routes each token to a single expert; its largest model, Switch-C, has 1,571 billion parameters and 2,048 experts." }
  - { name: "GLaM", year: 2021, note: "1.2 trillion parameters, 64 experts per MoE layer and 2 used per token, so 96.6 billion parameters (8%) are active per token." }
  - { name: "Mixtral 8x7B", year: 2023, note: "Open-weight model with 8 experts per layer and 2 used per token: 47B total, 13B active parameters." }
  - { name: "DeepSeek-V3", year: 2024, note: "671B total and 37B activated parameters per token; each MoE layer has 1 shared and 256 routed experts, 8 of them routed per token, with auxiliary-loss-free load balancing." }

resources:
  - { kind: paper, title: "Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer (Shazeer et al., 2017)", url: "https://arxiv.org/abs/1701.06538" }
  - { kind: paper, title: "Mixtral of Experts (Jiang et al., 2024)", url: "https://arxiv.org/abs/2401.04088" }
  - { kind: paper, title: "Switch Transformers (Fedus et al., 2021)", url: "https://arxiv.org/abs/2101.03961" }
  - { kind: blog, title: "Mixture of Experts Explained (Hugging Face)", url: "https://huggingface.co/blog/moe" }
  - { kind: video, title: "Mixture of Experts, visually explained", url: "https://www.youtube.com/watch?v=sOPDGQjFcuM" }

learn_first:
  - { label: "The Transformer block & its feed-forward layer", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "Softmax & how gating weights are formed", href: "/learn/concepts/softmax", tag: "Math" }
  - { label: "Scaling laws — why capacity vs. cost is the whole game", href: "/models/architecture/scaling-laws", tag: "Concept" }

tags: [moe, sparse, routing, llm]
updated: '2026-11-23'
sources:
  - { url: "https://doi.org/10.1162/neco.1991.3.1.79", title: "Adaptive Mixtures of Local Experts (Jacobs, Jordan, Nowlan, Hinton; Neural Computation 3(1), 79-87, 1991)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1701.06538", title: "Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer (Shazeer et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2006.16668", title: "GShard: Scaling Giant Models with Conditional Computation and Automatic Sharding (Lepikhin et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2101.03961", title: "Switch Transformers: Scaling to Trillion Parameter Models with Simple and Efficient Sparsity (Fedus et al., 2021)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2112.06905", title: "GLaM: Efficient Scaling of Language Models with Mixture-of-Experts (Du et al., 2021)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.95 }
  - { url: "https://mistral.ai/news/mixtral-of-experts", title: "Mixtral of experts (Mistral AI announcement, 11 December 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2412.19437", title: "DeepSeek-V3 Technical Report (DeepSeek-AI, 2024)", trust: 0.95 }
---

## What Changed

In a **dense** Transformer every token passes through the same feed-forward network in every
layer, so every parameter of that network does work for every token. A **mixture-of-experts
(MoE)** layer keeps several feed-forward networks, the **experts**, and a small **router** that
sends each token to only a few of them.

How to read the table above and the key numbers below:

- **Parameters** are the learned numbers of a model; "B" means billion.
- **Total parameters** are all the numbers stored; **active parameters** are the ones a single
  token actually passes through.
- Adding experts raises the total but **not the work per token**, as long as k, the number of
  experts each token uses, stays the same.
- The memory row is the catch: every expert must be stored, because any token may be sent to any
  of them.

## Key Ideas

- **Experts:** several feed-forward networks side by side in one layer.
- **Router:** a learned linear layer scores every expert for each token and keeps the top k.
- **Sparse activation:** computation per token follows the active parameters; memory follows the
  total.
- **Load balancing:** Switch Transformer adds an auxiliary loss to spread tokens across experts;
  DeepSeek-V3 adjusts a per-expert bias instead.

## How It Works

The figure above is DeepSeek-V3, one catalog model of this architecture. Its two blocks named
**Router** and **Experts** are the MoE layer; select a block to open it. Everything else in the
block (normalisation, attention, the residual additions) works as in a dense Transformer.

A **token** is a piece of text, a word or part of a word. Inside the model each token is carried
as a **vector**, a list of numbers (4096 in Mixtral, 7168 in DeepSeek-V3).

1. **In: one token's vector.** It arrives from the attention sub-layer and its normalisation.
   Switch Transformer replaces the dense feed-forward layer of the Transformer at exactly this
   point; attention is not changed.
2. **Router: score every expert.** The router multiplies the vector by a learned matrix and gets
   **one score per expert**. Mixtral writes this as $x \cdot W_g$; Switch Transformer as $h(x) = W_r \cdot x$.
3. **Keep the top k.** Only the k highest scores stay. Mixtral sets every other score to minus
   infinity, so those experts get weight 0. **k is a setting:** 1 in Switch Transformer, 2 in
   Mixtral, 8 of 256 routed experts in DeepSeek-V3.
4. **Turn the kept scores into weights.** Mixtral applies a **softmax** over the kept scores, so
   the weights are positive and add up to 1. DeepSeek-V3 instead passes each score through a
   **sigmoid** (a curve that maps any number into 0 to 1) and divides each selected value by
   their sum.
5. **Experts: run only the chosen ones.** Each expert is a feed-forward network of its own
   (SwiGLU in Mixtral). Shazeer et al. state the rule that makes the layer cheap: wherever the
   weight is 0, the expert's output need not be computed.
6. **Mix and pass on.** The chosen experts' outputs are added up, each multiplied by its weight.
   In DeepSeek-V3 the output of the **shared expert**, which runs for every token, is added too.
   The result joins the residual stream (the "+" circle) and goes to the next block.
7. **During training: keep the load even.** If the router sent most tokens to a few experts, the
   rest would sit idle. Switch Transformer adds an **auxiliary loss**, $\alpha \cdot N \cdot \sum_i f_i \cdot P_i$ with
   $\alpha = 10^{-2}$, which is smallest when tokens are spread evenly. DeepSeek-V3 adds a **bias** to each
   expert's score for choosing the top k only, lowering it for overloaded experts and raising it
   for underloaded ones.

In the auxiliary loss, $N$ is the number of experts, $f_i$ is the share of tokens sent to expert $i$,
and $P_i$ is the share of router probability given to expert $i$.

## Worked Example

**One token, four experts, top 2.** The router scores are made up and small so every step can be
checked by hand; k = 2 is Mixtral's setting.

The router gives this token the scores (1.0, 2.5, 0.5, 2.0) for experts 1 to 4.

1. **Keep the top 2:** Expert 2 (2.5) and Expert 4 (2.0). Experts 1 and 3 get weight 0 and are
   **never run** for this token.
2. **Softmax over the kept scores:**
   - $e^{2.5} \approx 12.182$ and $e^{2.0} \approx 7.389$; the total is 19.572.
   - Weights: 12.182 / 19.572 ≈ **0.622** for Expert 2 and 7.389 / 19.572 ≈ **0.378** for
     Expert 4. They add up to 1.
3. **The same weights, the other way round** (this is what the figure draws):
   - A softmax over all four scores gives 0.114, 0.509, 0.069 and 0.309.
   - The two kept experts hold 0.509 + 0.309 = 0.818 of the total.
   - Rescaled: 0.509 / 0.818 ≈ **0.622** and 0.309 / 0.818 ≈ **0.378**, as in step 2.
4. **Run the two experts.** Suppose Expert 2 returns (1, 0) and Expert 4 returns (0, 2); a real
   expert returns as many numbers as the token vector has.
5. **Mix:** 0.622 × (1, 0) + 0.378 × (0, 2) = (0.622, 0.756) ≈ **(0.62, 0.76)**, the layer's
   output for this token.

**DeepSeek-V3's version of step 2:** the same two experts, with a sigmoid in place of the
exponential. sigmoid(2.5) ≈ 0.924 and sigmoid(2.0) ≈ 0.881; their sum is 1.805; the weights are
0.924 / 1.805 ≈ **0.512** and 0.881 / 1.805 ≈ **0.488**.

**What this means:** the token used **2 of the 4** experts, so half of the experts' work was
skipped while all four experts stay in memory. Mixtral uses 2 of 8 experts in every layer, which
is how 47B stored parameters become 13B used per token.
