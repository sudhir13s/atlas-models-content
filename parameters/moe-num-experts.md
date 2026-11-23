---
id: parameter/moe-num-experts
type: parameter
slug: moe-num-experts
schema_version: 2.9
title: "Number of Experts (num_experts)"
name: num_experts
priority: 1
importance: critical
applies_to: [moe]
range: "8 — 2,048 experts"
controls: "How many expert feed-forward blocks each mixture-of-experts layer stores; it sets the total parameters, while the experts used per token set the computation."
effects:
  decrease:
    - "Fewer stored parameters, in a straight line: 226,590,720 fewer per expert in Qwen3-30B-A3B"
    - "Almost the same active parameters per token: only the router shrinks, by 98,304 per expert"
    - "Less memory to hold the model"
  increase:
    - "More stored parameters, in a straight line"
    - "Computation per token almost unchanged when experts per token stay fixed (Mixtral paper)"
    - "Switch Transformer calls the expert count the most efficient dimension for scaling, with diminishing returns"
scale:
  min: 2
  max: 4096
  typical_min: 8
  typical_max: 2048
  spacing: log
typical_values:
  - setting: "Mixtral-8x7B, num_local_experts (config.json)"
    value: "8"
    source: "https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json"
  - setting: "DeepSeek-V2-Lite, n_routed_experts, plus 2 shared (config.json)"
    value: "64"
    source: "https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json"
  - setting: "Qwen3-30B-A3B (config.json)"
    value: "128"
    source: "https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json"
  - setting: "DeepSeek-V3, n_routed_experts, plus 1 shared (config.json)"
    value: "256"
    source: "https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json"
  - setting: "Switch-C, 1,571B parameters (Switch Transformer paper, Table 9)"
    value: "2048"
    source: "https://arxiv.org/abs/2101.03961"
mistakes:
  - mistake: "Assuming more experts means more computation per token"
    why: >-
      The Mixtral paper says that increasing the number of experts while keeping the experts used per
      token fixed raises the parameter count while keeping the computational cost effectively
      constant. Going from 64 to 128 experts adds **14.5B** stored parameters to Qwen3-30B-A3B but only
      **6.3M** active ones.
    fix: "Read num_experts_per_tok for the computation and num_experts for the stored size."
  - mistake: "Sizing memory from the active parameters"
    why: >-
      Every expert must be held. The Mixtral paper notes that its memory costs follow the sparse
      parameter count, **47B**, not the **13B** active ones.
    fix: "Budget memory for the total count, as in the worked example."
  - mistake: "Reading the key under one name only"
    why: >-
      The same quantity has three names: num_experts in Qwen3-MoE, num_local_experts in Mixtral
      (Transformers maps num_experts to it) and n_routed_experts in DeepSeek-V3, where it leaves out
      the shared expert.
    fix: "Look for all three names, and add shared experts separately."
related: [parameter/moe-top-k, parameter/moe-expert-ffn-size, parameter/moe-shared-experts, parameter/intermediate-size-ffn]
visual:
  kind: moe_sweep
  model: { name: "Qwen3-30B-A3B", vocab_size: 151936, hidden_size: 2048, intermediate_size: 6144, num_hidden_layers: 48, num_attention_heads: 32, num_key_value_heads: 4, head_dim: 128, tie_word_embeddings: false, ffn_matrices: 3, qk_norm: true, num_experts: 128, num_experts_per_tok: 8, moe_intermediate_size: 768 }
  vary: num_experts
  values: [16, 32, 64, 128, 256]
  caption: "Total and active parameters of Qwen3-30B-A3B, computed from its config.json, if only num_experts changed (8 experts per token kept). Its own 128 experts are marked and give the card's 30.5B total and 3.3B activated; the other counts are not real Qwen3 models."
tags: [moe, mixture-of-experts, experts, capacity, parameters, sparse]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2101.03961", title: "Switch Transformers: Scaling to Trillion Parameter Models (Fedus et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1701.06538", title: "Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer (Shazeer et al., 2017)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2505.09388", title: "Qwen3 Technical Report (Qwen Team, 2025)", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json", title: "Qwen3-30B-A3B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-30B-A3B", title: "Qwen3-30B-A3B model card", trust: 0.9 }
  - { url: "https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json", title: "Mixtral-8x7B-Instruct-v0.1 configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json", title: "DeepSeek-V3 configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json", title: "DeepSeek-V2-Lite-Chat configuration (config.json)", trust: 0.95 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3_moe/modeling_qwen3_moe.py", title: "Hugging Face Transformers source: modeling_qwen3_moe.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/mixtral/configuration_mixtral.py", title: "Hugging Face Transformers source: configuration_mixtral.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py", title: "Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)", trust: 0.9 }
---

## Definition

**num_experts is how many expert feed-forward blocks each mixture-of-experts layer stores.**
Transformers describes it as the number of routed experts in MoE layers.

- **A mixture-of-experts (MoE) layer** replaces the single feed-forward block of a dense layer with
  several, the **experts**, each with its own weights (Shazeer et al.).
- **A router** scores the experts for each token and picks a few; the rest do nothing for that token.
- **Total against active:** the Mixtral paper calls everything stored the total (sparse) count and
  what one token uses the active count.
- **The figure** shows the split: as experts are added, the total climbs and the active count
  barely moves.

**Analogy:** a hospital with many specialists, where each patient sees only a couple of them. The
analogy shows the idea only; the exact counts are under Calculation.

## How It Works

**Store many, run a few.**

1. **Score.** The router, a matrix of hidden_size × num_experts, gives each expert a score per token.
2. **Pick.** In Transformers' Qwen3-MoE code, a softmax over the scores is followed by the top
   num_experts_per_tok experts, whose weights are rescaled to add up to 1.
3. **Run.** Only the picked experts process the token; their outputs are added with those weights.
4. **Balance.** Shazeer et al. observed the router drifting to always favour the same few experts,
   and added a loss to keep their use even; the Qwen3 report uses a global-batch balancing loss.

**Scale of the idea:** Shazeer et al. used up to **131,072** experts; Switch Transformer's largest
model has **2,048**.

## Calculation

**How the expert count enters the total.**

In words: every layer stores all experts and a router; a token uses k experts and the router.

$$
\text{total} = S + L\,(E\,F + d\,E) \qquad \text{active} = S + L\,(k\,F + d\,E) \qquad F = 3\,d\,f
$$

| Symbol | Meaning |
| --- | --- |
| $S$ | everything outside the experts and router: embedding, attention, norms, output head |
| $L$ | num_hidden_layers |
| $E$, $k$ | num_experts, num_experts_per_tok |
| $d$, $f$ | hidden_size, moe_intermediate_size |
| $F$ | one expert: gate, up and down matrices |

For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Qwen3-30B-A3B, from its config.json** (the numbers on its catalog page): hidden_size **2048**,
**48** layers, **128** experts of width **768**, **8** per token, untied head.

1. **One expert:** 3 × 2048 × 768 = **4,718,592**.
2. **All experts, one layer:** 128 × 4,718,592 = **603,979,776**; router 2048 × 128 = **262,144**.
3. **Attention per layer:** 2 × 2048 × 4096 + 2 × 2048 × 512 = **18,874,368**; norms **4,352**.
4. **Embedding and head:** 2 × 151,936 × 2048 = **622,329,856**, plus a final norm of 2048.
5. **Total:** 48 × 623,120,640 + 622,331,904 = **30,532,122,624**, the card's 30.5B.
6. **Active:** 48 × (18,874,368 + 8 × 4,718,592 + 262,144 + 4,352) + 622,331,904 =
   **3,353,032,704**, the card's 3.3B activated.
7. **Experts' share of the total:** 48 × 603,979,776 ÷ 30,532,122,624 ≈ **95.0%**.

**What this means:** each added expert costs 48 × (4,718,592 + 2,048) = **226,590,720** stored
parameters but only **98,304** active ones, through the router.

## What To Watch

- **num_experts_per_tok.** It, not the expert count, sets the computation per token.
- **The key's name.** num_experts, num_local_experts or n_routed_experts.
- **Dense layers.** Some models keep plain feed-forward blocks in a few layers, such as DeepSeek-V3's
  first three; Qwen3-30B-A3B has none.
