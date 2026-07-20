---
id: architecture-era/mixture-of-experts
type: architecture-era
slug: mixture-of-experts
schema_version: 1.0
title: "Mixture of Experts (Sparse MoE)"
name: "Mixture of Experts"
era: post-transformer
era_range: "2021–2026"
year: 2021
family: llm
core_innovation: "Replace the dense feed-forward block with many experts and a router that activates only a few per token — so total capacity grows without growing the cost of any single forward pass."
description: "Sparse transformers where total parameters greatly exceed the parameters active per token."
key_features: ["Expert feed-forward blocks", "Top-k token routing", "Sparse activation", "Total ≫ active parameters"]
key_models: ["Switch Transformer (2021)", "Mixtral 8x7B (2024)", "DeepSeek-V3 (2024)"]
paper_title: "Mixtral of Experts"
paper_url: "https://arxiv.org/abs/2401.04088"
influenced_by: [architecture-era/transformer]
led_to: [architecture-era/reasoning-models]

eli5: "Instead of every part of the model working on every word, the model keeps a team of specialists. For each word a little router picks the two specialists most likely to help, and only they do the work. You get the knowledge of a huge model while paying for a small one."

analogy:
  title: "A hospital with specialists and a triage nurse"
  body: "A dense model is one exhausted general doctor who personally handles every patient. A mixture-of-experts is a hospital: dozens of specialists — cardiology, orthopedics, neurology — sit ready, and a triage nurse at the door reads each patient and sends them to the two most relevant specialists. The hospital collectively knows far more than any one doctor, but each patient only occupies two specialists, not all of them. The router is the nurse; the experts are the specialists; 'top-k' is how many specialists each patient sees."

narrative:
  motivation: "Scaling a transformer means making its feed-forward layers bigger, but in a dense model every parameter fires for every token — so doubling capacity doubles the compute of every forward pass. That coupling is the wall: quality wants more parameters, budgets want fewer FLOPs per token, and dense models force you to pay for both at once."
  approach: "Break the coupling. Replace the single feed-forward block with N parallel expert blocks and a tiny router. For each token the router scores the experts and sends the token to only the top-k (often k=2). Capacity now scales with the number of experts; cost scales only with k. Total parameters and active parameters become two different numbers you can tune independently."
  solution: "The router is a small learned gate: it produces a score per expert, keeps the top-k, softmax-normalizes those into weights, and the layer's output is the weighted sum of just those experts' outputs. Because routing is discrete, experts can specialize and none of the others run. A load-balancing auxiliary loss nudges the router to spread tokens evenly so experts don't collapse into one favorite or starve unused."
  outcome: "Mixture-of-experts decouples what a model knows from what it costs to run. Mixtral 8x7B carries ~47B total parameters but activates only ~13B per token, matching much larger dense models at a fraction of the inference cost. DeepSeek-V3 pushed this to hundreds of billions of total parameters with a small active fraction. Sparse routing is now the default way frontier labs buy capacity cheaply."

build_steps:
  - { title: "1. Start from a transformer block", detail: "Keep attention exactly as it is. The only thing that changes is the feed-forward sublayer that follows attention." }
  - { title: "2. Clone the feed-forward into N experts", detail: "Instead of one feed-forward network (FFN), place N independent FFNs side by side. Each can become a specialist. Total parameters jump by roughly N×." }
  - { title: "3. Add a router (gate)", detail: "A small linear layer reads each token vector and outputs one score per expert — a guess at which experts will help this particular token." }
  - { title: "4. Keep only the top-k", detail: "Sort the scores, keep the best k experts (often k=2), and softmax those k scores into mixing weights. The other N−k experts are skipped entirely for this token." }
  - { title: "5. Mix the chosen experts", detail: "Run the token through only its k experts and combine their outputs as a weighted sum using the router's weights. That blend is the layer's output." }
  - { title: "6. Balance the load", detail: "Add an auxiliary loss that rewards even usage across experts, so the router doesn't send every token to the same one (collapse) or leave experts untrained (starvation)." }

key_equation:
  latex: "y = \\sum_{i \\in \\mathrm{TopK}(x)} g_i(x)\\, E_i(x)"
  plain: "The output is a weighted sum over only the top-k experts the router picked. Each chosen expert Eᵢ processes the token, the router weight gᵢ says how much to trust it, and everything outside the top-k contributes exactly zero — that's what makes it sparse."
  terms:
    - { symbol: "x", meaning: "the input token vector arriving at the layer" }
    - { symbol: "E_i(x)", meaning: "the output of expert i (an independent feed-forward network)" }
    - { symbol: "g_i(x)", meaning: "the router's gating weight for expert i — nonzero only if i is in the top-k, and the weights over the top-k sum to 1" }
    - { symbol: "TopK(x)", meaning: "the set of the k highest-scoring experts for this token; every other expert is skipped" }

strengths:
  - "Decouples capacity from cost — total parameters (knowledge) can grow far faster than active parameters (FLOPs per token)."
  - "Experts specialize, so the model can hold more distinct skills than a same-cost dense network."
  - "Matches much larger dense models at a fraction of the inference and training compute."
  - "Drops into the transformer with only the feed-forward block changed — attention and the rest are untouched."

weaknesses:
  - "High memory footprint: all experts must be loaded even though only a few run, straining VRAM."
  - "Routing is unstable — without a load-balancing loss experts collapse or starve, and the discrete top-k complicates training."
  - "Uneven expert usage causes load imbalance across GPUs, hurting throughput at serving time."
  - "Fine-tuning and distillation are trickier than for a plain dense model."

diagram: |
  flowchart TD
    T["Token vector"] --> R["Router (gating network)"]
    R -->|"top-1 weight"| E2["Expert 2"]
    R -->|"top-2 weight"| E5["Expert 5"]
    R -. "skipped" .-> E1["Expert 1"]
    R -. "skipped" .-> E3["Expert 3"]
    R -. "skipped" .-> E4["Expert 4"]
    E2 --> S["Weighted sum"]
    E5 --> S
    S --> OUT["Layer output"]
diagram_caption: "The router scores all experts but only the top-k (here 2 of 5) actually run; their outputs are blended by the router's weights. The rest are skipped, which is where the compute savings come from."

notable_models:
  - { name: "Switch Transformer", year: 2021, note: "Simplified routing to a single expert (top-1) and scaled to over a trillion parameters, proving sparse routing trains stably at scale." }
  - { name: "GLaM", year: 2021, note: "Showed a sparse MoE could match dense GPT-3 quality using far less energy per token." }
  - { name: "Mixtral 8x7B", year: 2024, note: "Open-weight MoE with ~47B total but ~13B active parameters, rivaling much larger dense models — the model that mainstreamed MoE openly." }
  - { name: "DeepSeek-V3", year: 2024, note: "Scaled fine-grained experts to hundreds of billions of total parameters with a small active fraction and strong load balancing." }

resources:
  - { kind: paper, title: "Mixtral of Experts (Jiang et al., 2024)", url: "https://arxiv.org/abs/2401.04088" }
  - { kind: paper, title: "Switch Transformers (Fedus et al., 2021)", url: "https://arxiv.org/abs/2101.03961" }
  - { kind: blog, title: "Mixture of Experts Explained (Hugging Face)", url: "https://huggingface.co/blog/moe" }
  - { kind: video, title: "Mixture of Experts, visually explained", url: "https://www.youtube.com/watch?v=sOPDGQjFcuM" }

learn_first:
  - { label: "The Transformer block & its feed-forward layer", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "Softmax & how gating weights are formed", href: "/learn/concepts/softmax", tag: "Math" }
  - { label: "Scaling laws — why capacity vs. cost is the whole game", href: "/models/architecture/scaling-laws", tag: "Concept" }

tags: [moe, sparse, routing, llm]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al.)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2101.03961", title: "Switch Transformers (Fedus et al.)", trust: 0.9 }
  - { url: "https://huggingface.co/blog/moe", title: "Mixture of Experts Explained (Hugging Face)", trust: 0.9 }
---

## What Changed
Mixture-of-experts (MoE) keeps the transformer backbone but replaces each dense feed-forward block with a
set of N expert feed-forward networks (FFNs) plus a lightweight router. For every token the router selects
the top-k experts (often k=2), so only a fraction of the network's parameters are active per token. This
decouples model capacity (total parameters) from inference cost (active parameters): Mixtral 8x7B has ~47B
total parameters but activates ~13B per token. It descends from the dense transformer and the Switch
Transformer's routing, and underpins several modern frontier models.

## Key Ideas
- **Experts** — multiple parallel FFNs; capacity scales with expert count.
- **Router** — a small gating network scores experts per token and picks the top-k.
- **Sparse activation** — compute stays close to a dense model of the *active* size, while quality
  tracks the much larger *total* size.
- **Load balancing** — an auxiliary loss spreads tokens across experts so none collapse or starve.
