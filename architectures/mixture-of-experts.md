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
core_innovation: "Replace a dense feed-forward block with many expert feed-forward networks and a learned router that activates only a few per token, so total parameters grow without a matching growth in computation per token."
description: "Sparse transformers where total parameters greatly exceed the parameters active per token."
key_features: ["Several expert feed-forward networks per MoE layer", "A learned router selects a few experts per token", "Sparse activation", "Total parameters far exceed active parameters"]
key_models: ["Switch Transformer (2021)", "Mixtral 8x7B (2023)", "DeepSeek-V3 (2024)"]
paper_title: "Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer"
paper_url: "https://arxiv.org/abs/1701.06538"
influenced_by: [architecture-era/transformer]
led_to: [architecture-era/reasoning-models]

eli5: "Instead of every part of the model working on every word, the model keeps a team of specialists. For each word a small router picks a few of them, and only those do the work. The model holds many parameters but uses only some of them for each word."

analogy:
  title: "A hospital with specialists and a triage nurse"
  body: "A dense model is one doctor who handles every patient. A mixture-of-experts is a hospital: many specialists sit ready, and a triage nurse at the door sends each patient to one or two of them. The hospital holds far more expertise than one doctor, but each patient occupies only a few specialists. The router is the nurse, the experts are the specialists, and 'top-k' is how many specialists each patient sees."

narrative:
  motivation: "In a dense model every parameter is used for every token, so adding parameters adds computation to every forward pass. Conditional computation, where only parts of the network are active per example, had been proposed to grow capacity without a proportional growth in computation."
  approach: "Shazeer et al. (2017) introduced the sparsely-gated mixture-of-experts layer: many feed-forward sub-networks (experts) and a trainable gating network that picks a sparse combination of them for each example. GShard (2020) and Switch Transformer (2021) applied such layers inside Transformers."
  solution: "The router is a learned linear layer that scores every expert for a token. Mixtral keeps the top two scores and applies a softmax over them; the layer's output is the weighted sum of those experts' outputs. Switch Transformer routes each token to a single expert and adds an auxiliary load-balancing loss; DeepSeek-V3 uses an auxiliary-loss-free balancing strategy."
  outcome: "Mixtral 8x7B has 47B parameters but uses 13B active parameters per token, and its authors report it outperforming or matching Llama 2 70B and GPT-3.5 on the benchmarks they evaluated. DeepSeek-V3 has 671B total parameters with 37B activated per token."

build_steps:
  - { title: "1. Start from a transformer block", detail: "Attention stays as it is. The feed-forward sub-layer after attention is what changes." }
  - { title: "2. Replace the feed-forward with N experts", detail: "Place N independent feed-forward networks side by side in that layer. Mixtral uses 8 per layer." }
  - { title: "3. Add a router (gate)", detail: "A small linear layer reads each token vector and outputs one score per expert." }
  - { title: "4. Keep only the top-k", detail: "Keep the k highest-scoring experts (Switch Transformer uses k = 1, Mixtral k = 2) and turn their scores into mixing weights. The other experts are not computed for this token." }
  - { title: "5. Mix the chosen experts", detail: "Run the token through its k experts and combine their outputs as a weighted sum using the router's weights." }
  - { title: "6. Balance the load", detail: "Keep tokens spread across experts: Switch Transformer adds an auxiliary load-balancing loss; DeepSeek-V3 uses an auxiliary-loss-free strategy." }

key_equation:
  latex: "y = \\sum_{i \\in \\mathrm{TopK}(x)} g_i(x)\\, E_i(x)"
  plain: "The output is a weighted sum over only the top-k experts the router picked. Each chosen expert Eᵢ processes the token, the router weight gᵢ sets its share, and experts outside the top-k contribute nothing, which is what makes the layer sparse."
  terms:
    - { symbol: "x", meaning: "the input token vector arriving at the layer" }
    - { symbol: "E_i(x)", meaning: "the output of expert i (an independent feed-forward network)" }
    - { symbol: "g_i(x)", meaning: "the router's gating weight for expert i; nonzero only if i is in the top-k (in Mixtral, a softmax over the top-k scores)" }
    - { symbol: "TopK(x)", meaning: "the set of the k highest-scoring experts for this token; every other expert is skipped" }

strengths:
  - "Total parameters can grow much faster than the computation per token."
  - "GLaM's authors report better zero-shot and one-shot results than GPT-3 at one third of its training energy and half its inference FLOPs."
  - "Only the feed-forward sub-layers change; attention is untouched."

weaknesses:
  - "Memory cost follows the total parameter count: serving Mixtral needs memory for all 47B parameters, not the 13B active."
  - "Routing adds overhead, and uneven expert assignment complicates load balancing across devices (Mixtral paper)."
  - "Switch Transformer's authors cite complexity, communication costs and training instability as obstacles to adoption."

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
diagram_caption: "The router scores all experts but only the top-k (here 2 of 5) run; their outputs are combined with the router's weights. The skipped experts are not computed for this token."

notable_models:
  - { name: "Switch Transformer", year: 2021, note: "Routes each token to a single expert; trained models of up to a trillion parameters." }
  - { name: "GLaM", year: 2021, note: "A 1.2-trillion-parameter sparse model; its authors report better zero-shot and one-shot results than GPT-3 with one third of the training energy." }
  - { name: "Mixtral 8x7B", year: 2023, note: "Open-weight model with 8 experts per layer and 2 active per token: 47B total, 13B active parameters." }
  - { name: "DeepSeek-V3", year: 2024, note: "671B total, 37B active parameters per token, with DeepSeekMoE experts and an auxiliary-loss-free load-balancing strategy." }

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
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/1701.06538", title: "Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer (Shazeer et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2006.16668", title: "GShard: Scaling Giant Models with Conditional Computation and Automatic Sharding (Lepikhin et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2101.03961", title: "Switch Transformers (Fedus et al.)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2112.06905", title: "GLaM: Efficient Scaling of Language Models with Mixture-of-Experts (Du et al., 2021)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al.)", trust: 0.95 }
  - { url: "https://mistral.ai/news/mixtral-of-experts", title: "Mixtral of experts (Mistral AI announcement, 11 December 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2412.19437", title: "DeepSeek-V3 Technical Report (DeepSeek-AI, 2024)", trust: 0.95 }
  - { url: "https://huggingface.co/blog/moe", title: "Mixture of Experts Explained (Hugging Face)", trust: 0.9 }
---

## What Changed
Mixture-of-experts (MoE) replaces a dense feed-forward block with a set of expert feed-forward networks
plus a small router.

- The sparsely-gated MoE layer comes from **Shazeer et al. (2017)**; GShard and Switch Transformer
  applied it inside Transformers.
- For every token the router selects a few experts (Switch Transformer one, Mixtral two), so only part
  of the parameters are active per token.
- Mixtral 8x7B has 47B total parameters and uses 13B per token.

## Key Ideas
- **Experts**: several parallel feed-forward networks; capacity grows with the number of experts.
- **Router**: a small gating network scores experts per token and keeps the top-k.
- **Sparse activation**: computation per token follows the active parameters; memory follows the total.
- **Load balancing**: Switch Transformer uses an auxiliary loss to spread tokens across experts;
  DeepSeek-V3 balances load without an auxiliary loss.
