---
id: parameter/moe-shared-experts
type: parameter
slug: moe-shared-experts
schema_version: 3.1
title: Shared Experts (n_shared_experts)
name: n_shared_experts
priority: 4
importance: important
applies_to:
- moe
range: 0 — 4
controls: How many experts every token passes through regardless of the router, on top of the routed experts it is sent to.
effects:
  decrease:
  - 'Fewer always-used parameters per token: one expert less in every MoE layer'
  - With none, as in Mixtral and Qwen3-MoE, every expert is routed
  - DeepSeekMoE found that removing the shared expert and routing to one more raised Pile loss from 1.808 to 2.414
  increase:
  - 'More parameters that every token uses: each shared expert counts in both the total and the active count'
  - DeepSeekMoE cut the routed experts per token by the same number to keep computation constant
  - In its ablation, 1, 2 and 4 shared experts of 64 gave Pile losses of 1.808, 1.806 and 1.811
scale:
  min: 0
  max: 8
  typical_min: 0
  typical_max: 4
  spacing: linear
typical_values:
- setting: Mixtral-8x7B, all 8 experts routed (Mixtral paper)
  value: '0'
  source: https://arxiv.org/abs/2401.04088
- setting: Qwen3-30B-A3B, which excludes shared experts (Qwen3 report)
  value: '0'
  source: https://arxiv.org/abs/2505.09388
- setting: DeepSeek-V3, with 256 routed experts (config.json)
  value: '1'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
- setting: DeepSeek-V2-Lite, with 64 routed experts (config.json)
  value: '2'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json
- setting: DeepSeekMoE 145B, with 128 routed experts (DeepSeekMoE paper)
  value: '4'
  source: https://arxiv.org/abs/2401.06066
mistakes:
- mistake: Thinking shared experts are chosen by the router
  why: DeepSeekMoE assigns every token to the shared experts deterministically, regardless of the router. In Transformers' DeepSeek-V3 code the shared block runs on every token and its output is added to the routed experts'.
  fix: Count shared experts as always active.
- mistake: Assuming a shared expert adds computation on top of the same routing
  why: In DeepSeekMoE the routed experts used per token were reduced by the number of shared experts to keep the computation constant. DeepSeek-V3's paper says that, at decoding, each token in effect selects **9** experts, the shared one treated as a routed one.
  fix: Compare models by active parameters, counting shared and routed experts together.
- mistake: Counting n_shared_experts as separate blocks
  why: Transformers' DeepSeek-V3 code builds one feed-forward block of width moe_intermediate_size × n_shared_experts. Its parameters equal n_shared_experts experts, but it is one block.
  fix: Count the shared parameters as n_shared_experts × 3 × hidden_size × moe_intermediate_size.
related:
- parameter/moe-num-experts
- parameter/moe-top-k
- parameter/moe-expert-ffn-size
visual:
  kind: expert_layer
  model:
    name: DeepSeek-V3
    hidden_size: 7168
    moe_intermediate_size: 2048
    n_routed_experts: 256
    num_experts_per_tok: 8
    n_shared_experts: 1
  caption: 'One of DeepSeek-V3''s 58 mixture-of-experts layers, computed from its config.json: what is stored, and what each token uses. The single shared expert is a sliver of the stored layer but one ninth of the experts each token runs.'
tags:
- moe
- mixture-of-experts
- shared-expert
- routing
- parameters
- sparse
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/2401.06066
  title: 'DeepSeekMoE: Towards Ultimate Expert Specialization in Mixture-of-Experts Language Models (Dai et al., 2024)'
  trust: 0.9
- url: https://arxiv.org/abs/2412.19437
  title: DeepSeek-V3 Technical Report (DeepSeek-AI, 2024)
  trust: 0.9
- url: https://arxiv.org/abs/2401.04088
  title: Mixtral of Experts (Jiang et al., 2024)
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: Qwen3 Technical Report (Qwen Team, 2025)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
  title: DeepSeek-V3 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json
  title: DeepSeek-V2-Lite-Chat configuration (config.json)
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v3/modeling_deepseek_v3.py
  title: 'Hugging Face Transformers source: modeling_deepseek_v3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py
  title: 'Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)'
  trust: 0.9
---

## Definition

**n_shared_experts is how many experts every token passes through, whatever the router picks.**
Transformers describes it as the number of shared experts.

- **Routed experts** are chosen per token by the router; **shared experts** are always used.
- **Why:** DeepSeekMoE argues that routed experts otherwise each relearn common knowledge, and
  isolates shared experts to hold it, so the routed ones can specialise.
- **Who uses them:** DeepSeek-V3 has **1**, DeepSeek-V2-Lite **2**; Mixtral and Qwen3-MoE have none.
- **The figure** shows one DeepSeek-V3 layer: the shared expert is **0.4%** of what is stored but
  **11.1%** of the experts each token runs.

**Analogy:** a general practitioner every patient sees, beside the specialists the triage desk
picks. The analogy shows the idea only; the exact counts are under Calculation.

## How It Works

**Route some, always run the rest.**

1. **Route.** The router picks num_experts_per_tok routed experts; DeepSeek-V3 scores them with a
   sigmoid and normalises the chosen scores.
2. **Run the shared block.** In Transformers' DeepSeek-V3 code it is one gated feed-forward block of
   width moe_intermediate_size × n_shared_experts, run on every token.
3. **Add.** The weighted routed outputs and the shared output are added to the token's vector.
4. **Keep the cost.** DeepSeekMoE lowers the routed experts per token by the number of shared ones;
   DeepSeekMoE 16B routes each token to **2** shared and **6** of 64 routed experts.

**Is it needed?** In DeepSeekMoE's test, removing the shared expert and routing to one more raised
the Pile loss from **1.808** to **2.414** at the same computation.

## Calculation

**What shared experts add to one layer.**

In words: each shared expert is the size of a routed one and counts in both the stored and the
per-token parameters.

$$
F = 3\,d\,f \qquad \text{stored} = (E_r + E_s)\,F + d\,E_r \qquad \text{per token} = (k + E_s)\,F + d\,E_r
$$

| Symbol | Meaning |
| --- | --- |
| $d$, $f$ | hidden_size, moe_intermediate_size |
| $E_r$, $E_s$ | n_routed_experts, n_shared_experts |
| $k$ | num_experts_per_tok |
| $d\,E_r$ | the router |

For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**One MoE layer of DeepSeek-V3, from its config.json** (the numbers on its catalog page):
hidden_size **7168**, moe_intermediate_size **2048**, **256** routed experts, **8** per token,
**1** shared.

1. **One expert:** 3 × 7168 × 2048 = **44,040,192**.
2. **Routed, stored:** 256 × 44,040,192 = **11,274,289,152**; router 7168 × 256 = **1,835,008**.
3. **Shared:** 1 × 44,040,192 = **44,040,192**, stored and used by every token.
4. **Used per token:** 8 × 44,040,192 + 44,040,192 + 1,835,008 = **398,196,736**.
5. **Shared share of the experts used:** 44,040,192 ÷ (9 × 44,040,192) = **1/9 ≈ 11.1%**.
6. **Whole model:** 61 layers, the first 3 dense, so 58 MoE layers hold 58 × 44,040,192 =
   **2,554,331,136** shared parameters.

**What this means:** a shared expert costs little to store but is in every token's computation.
Counted with its attention, the whole model comes to the paper's **671B** total and about **37B**
active.

## What To Watch

- **Dense layers.** DeepSeek-V3 replaces the feed-forward block with MoE in all but the first three
  layers (first_k_dense_replace 3).
- **num_experts_per_tok.** In DeepSeek configurations it counts routed experts only.
- **A missing key.** Mixtral and Qwen3-MoE configurations have no shared experts at all.
