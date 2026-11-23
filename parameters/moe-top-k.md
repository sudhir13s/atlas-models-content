---
id: parameter/moe-top-k
type: parameter
slug: moe-top-k
schema_version: 2.9
title: "Experts per Token (num_experts_per_tok)"
name: num_experts_per_tok
priority: 3
importance: critical
applies_to: [moe]
range: "1 — 8 experts"
controls: "How many experts the router sends each token to; it sets the active parameters and the computation per token, not the stored size."
effects:
  decrease:
    - "Fewer active parameters per token: Mixtral with 1 expert would use 7.24B instead of 12.88B"
    - "Total parameters unchanged"
    - "Switch Transformer routes to 1 expert, and reports that this preserves quality and cuts routing computation"
  increase:
    - "More active parameters, one expert's worth per step: 5.64B in Mixtral"
    - "Total parameters unchanged; with every expert used, active equals total"
    - "Mixtral's paper calls k the hyper-parameter that sets the compute per token"
scale:
  min: 1
  max: 32
  typical_min: 1
  typical_max: 8
  spacing: log
typical_values:
  - setting: "Switch Transformer, one expert per token (Fedus et al.)"
    value: "1"
    source: "https://arxiv.org/abs/2101.03961"
  - setting: "Mixtral-8x7B, of 8 experts (config.json)"
    value: "2"
    source: "https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json"
  - setting: "Sparsely-gated MoE, flat layers (Shazeer et al.)"
    value: "4"
    source: "https://arxiv.org/abs/1701.06538"
  - setting: "DeepSeek-V2-Lite, of 64 routed, plus 2 shared (config.json)"
    value: "6"
    source: "https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json"
  - setting: "Qwen3-30B-A3B, of 128 (config.json)"
    value: "8"
    source: "https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json"
  - setting: "DeepSeek-V3, of 256 routed, plus 1 shared (config.json)"
    value: "8"
    source: "https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json"
mistakes:
  - mistake: "Thinking the top-k changes the model's size"
    why: >-
      Every expert is stored whatever k is. The Mixtral paper separates the total count, which grows
      with the number of experts, from the active count, which grows with k. Mixtral's total stays
      **46,702,792,704** at k = 1, 2, 4 or 8.
    fix: "Change num_experts_per_tok to trade computation, not memory."
  - mistake: "Taking the weights of the chosen experts from a softmax over all experts"
    why: >-
      Mixtral defines the gate as Softmax(TopK(x · W_g)): only the top-k scores enter the softmax.
      Transformers' Mixtral and Qwen3-MoE code compute a softmax over all experts and then rescale
      the top k to add up to 1, which gives the same weights. DeepSeek-V3 uses a sigmoid instead, then
      normalises the chosen scores.
    fix: "Read the model's routing code before reasoning about the weights."
  - mistake: "Believing a router needs at least two experts per token to learn"
    why: >-
      Shazeer et al. conjectured that k > 1 was needed for useful gradients to the router. Fedus et
      al. route to a single expert in Switch Transformer and report that this preserves model quality.
    fix: "Treat k = 1 as a tested design, not a broken one."
related: [parameter/moe-num-experts, parameter/moe-expert-ffn-size, parameter/moe-shared-experts]
visual:
  kind: moe_sweep
  model: { name: "Mixtral-8x7B", vocab_size: 32000, hidden_size: 4096, intermediate_size: 14336, num_hidden_layers: 32, num_attention_heads: 32, num_key_value_heads: 8, head_dim: 128, tie_word_embeddings: false, ffn_matrices: 3, qk_norm: false, num_experts: 8, num_experts_per_tok: 2, moe_intermediate_size: 14336 }
  vary: num_experts_per_tok
  values: [1, 2, 4, 8]
  caption: "Total and active parameters of Mixtral-8x7B, computed from its config.json (its experts are intermediate_size 14,336 wide), if only num_experts_per_tok changed. Its own 2 is marked and gives the paper's 47B total and 13B active; with all 8, active equals total. The other values are not real Mixtral models."
tags: [moe, mixture-of-experts, routing, top-k, compute, sparse]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2101.03961", title: "Switch Transformers: Scaling to Trillion Parameter Models (Fedus et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1701.06538", title: "Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer (Shazeer et al., 2017)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2412.19437", title: "DeepSeek-V3 Technical Report (DeepSeek-AI, 2024)", trust: 0.9 }
  - { url: "https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json", title: "Mixtral-8x7B-Instruct-v0.1 configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json", title: "Qwen3-30B-A3B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json", title: "DeepSeek-V3 configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json", title: "DeepSeek-V2-Lite-Chat configuration (config.json)", trust: 0.95 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/mixtral/modeling_mixtral.py", title: "Hugging Face Transformers source: modeling_mixtral.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3_moe/modeling_qwen3_moe.py", title: "Hugging Face Transformers source: modeling_qwen3_moe.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v3/modeling_deepseek_v3.py", title: "Hugging Face Transformers source: modeling_deepseek_v3.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py", title: "Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)", trust: 0.9 }
---

## Definition

**num_experts_per_tok is how many experts each token is sent to, in every mixture-of-experts
layer.** Transformers describes it as the number of experts to route each token to, the top-k value
of token-choice routing.

- **The router** scores every expert for a token; the **top k** scores win.
- **Only those k experts run** for that token; their outputs are added, weighted by the router.
- **Active parameters** are what one token uses; k sets how many expert weights are among them.
- **The figure** shows Mixtral: the total stays flat while the active count climbs with k.

**Analogy:** a panel of eight judges, of whom each contestant is heard by two. The analogy shows the
idea only; the exact counts are under Calculation.

## How It Works

**Score, keep the top k, mix.**

1. **Score.** A linear layer gives one score per expert (Mixtral: x · W_g).
2. **Keep k.** Mixtral keeps the top 2 of 8 and turns them into weights with a softmax.
3. **Mix.** Each chosen expert, a SwiGLU block in Mixtral, processes the token; the outputs are added
   with their weights.
4. **Repeat per layer.** The choice is made again in every layer, so a token can use different
   experts at each one.

**Settings in use:** Switch Transformer uses **1**; Shazeer et al. used **4** in flat MoE layers;
Mixtral **2**; Qwen3-30B-A3B and DeepSeek-V3 **8**, DeepSeek-V3 also with one shared expert.

## Calculation

**How k enters the active count.**

In words: every layer adds k experts' worth of weights to what a token uses; the stored total does
not depend on k.

$$
\text{active} = S + L\,(k\,F + d\,E) \qquad \text{total} = S + L\,(E\,F + d\,E) \qquad F = 3\,d\,f
$$

| Symbol | Meaning |
| --- | --- |
| $S$ | embedding, attention, norms and output head |
| $L$ | num_hidden_layers |
| $k$, $E$ | num_experts_per_tok, number of experts |
| $d$, $f$ | hidden_size, expert width |

For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Mixtral-8x7B, from its config.json** (the numbers on its catalog page): hidden_size **4096**,
**32** layers, **8** experts of width **14,336**, **2** per token, vocab_size **32,000**.

1. **One expert:** 3 × 4096 × 14,336 = **176,160,768**.
2. **The rest of a layer:** attention **41,943,040**, router 4096 × 8 = **32,768**, norms **8,192**.
3. **Outside the layers:** embedding and head 2 × 32,000 × 4096 = **262,144,000**, final norm 4096.
4. **Total:** 32 × (41,943,040 + 8 × 176,160,768 + 32,768 + 8,192) + 262,148,096 =
   **46,702,792,704**, the paper's 47B.
5. **Active at k = 2:** 32 × (41,943,040 + 2 × 176,160,768 + 32,768 + 8,192) + 262,148,096 =
   **12,879,925,248**, the paper's 13B.
6. **At k = 1:** 12,879,925,248 − 32 × 176,160,768 = **7,242,780,672**; the total is unchanged.

**What this means:** each step of k adds 32 × 176,160,768 = **5,637,144,576** active parameters to
Mixtral and nothing to its size.

## What To Watch

- **The number of experts.** k cannot exceed it; at k equal to it, the model runs densely.
- **Shared experts.** In DeepSeek models they run for every token on top of k.
- **The router's weights.** Softmax, rescaled top-k or sigmoid: see the model's code.
