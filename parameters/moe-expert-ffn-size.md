---
id: parameter/moe-expert-ffn-size
type: parameter
slug: moe-expert-ffn-size
schema_version: 3.1
title: Expert FFN Size (moe_intermediate_size)
name: moe_intermediate_size
priority: 2
importance: critical
applies_to:
- moe
range: 768 — 14,336
controls: How wide each expert's feed-forward block is; it sets the size of every expert, so it scales both the stored and the active parameters.
effects:
  decrease:
  - 'Smaller experts: 3 × hidden_size fewer parameters per expert for each unit of width'
  - Fewer stored and fewer active parameters together, unlike fewer experts
  - DeepSeekMoE splits each expert into m narrower ones and activates m times as many, at the same cost
  increase:
  - Larger experts, in a straight line with the width
  - More active parameters per token as well as more stored ones
  - Mixtral's experts are full feed-forward blocks, 14,336 wide on a hidden size of 4,096
scale:
  min: 256
  max: 32768
  typical_min: 768
  typical_max: 14336
  spacing: log
typical_values:
- setting: Qwen3-30B-A3B, 128 experts (config.json)
  value: '768'
  source: https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json
- setting: DeepSeek-V2-Lite, 64 routed experts (config.json)
  value: '1408'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json
- setting: DeepSeek-V3, 256 routed experts (config.json)
  value: '2048'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
- setting: Mixtral-8x7B, 8 experts; the key is intermediate_size (config.json)
  value: '14336'
  source: https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json
mistakes:
- mistake: Reading intermediate_size as the expert width in a Qwen3-MoE model
  why: Qwen3-30B-A3B's config.json lists intermediate_size **6144** and moe_intermediate_size **768**. Transformers' Qwen3-MoE code uses intermediate_size only for layers listed as dense (mlp_only_layers), and that list is empty, so every expert is **768** wide.
  fix: Take the expert width from moe_intermediate_size where the key exists.
- mistake: Looking for moe_intermediate_size in every configuration
  why: 'Mixtral has no such key: Transformers'' Mixtral code builds each expert from intermediate_size, **14,336**.'
  fix: For Mixtral-style models, read intermediate_size as the expert width.
- mistake: Assuming wider experts grow only the stored size
  why: 'Each expert a token uses carries the full width, so the active count grows too. In Qwen3-30B-A3B, one unit of width adds 3 × 2048 = **6,144** parameters per expert: **37.7M** stored and **2.4M** active over 128 experts, 8 per token and 48 layers.'
  fix: Change the expert count to grow only the total; change the width to grow both.
related:
- parameter/moe-num-experts
- parameter/moe-top-k
- parameter/intermediate-size-ffn
- parameter/moe-shared-experts
visual:
  kind: moe_count
  models:
  - name: Mixtral-8x7B
    vocabulary_size: 32000
    hidden_size: 4096
    intermediate_size: 14336
    num_layers: 32
    attention_heads: 32
    kv_heads: 8
    head_size: 128
    tie_word_embeddings: false
    ffn_matrices: 3
    qk_norm: false
    num_experts: 8
    num_experts_per_tok: 2
    moe_intermediate_size: 14336
  - name: Qwen3-30B-A3B
    vocabulary_size: 151936
    hidden_size: 2048
    intermediate_size: 6144
    num_layers: 48
    attention_heads: 32
    kv_heads: 4
    head_size: 128
    tie_word_embeddings: false
    ffn_matrices: 3
    qk_norm: true
    num_experts: 128
    num_experts_per_tok: 8
    moe_intermediate_size: 768
  caption: 'Two designs computed from their config.json files: Mixtral''s 8 wide experts (14,336, its intermediate_size) and Qwen3-30B-A3B''s 128 narrow ones (768), stored against used per token. The totals and active counts match each maker''s published figures.'
tags:
- moe
- mixture-of-experts
- ffn
- expert
- width
- parameters
- sparse
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/2401.06066
  title: 'DeepSeekMoE: Towards Ultimate Expert Specialization in Mixture-of-Experts Language Models (Dai et al., 2024)'
  trust: 0.9
- url: https://arxiv.org/abs/2401.04088
  title: Mixtral of Experts (Jiang et al., 2024)
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: Qwen3 Technical Report (Qwen Team, 2025)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B/blob/main/config.json
  title: Qwen3-30B-A3B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/Qwen/Qwen3-30B-A3B
  title: Qwen3-30B-A3B model card
  trust: 0.9
- url: https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json
  title: Mixtral-8x7B-Instruct-v0.1 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
  title: DeepSeek-V3 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json
  title: DeepSeek-V2-Lite-Chat configuration (config.json)
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3_moe/modeling_qwen3_moe.py
  title: 'Hugging Face Transformers source: modeling_qwen3_moe.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/mixtral/modeling_mixtral.py
  title: 'Hugging Face Transformers source: modeling_mixtral.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py
  title: 'Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)'
  trust: 0.9
---

## Definition

**moe_intermediate_size is the width of each expert's feed-forward block.** Transformers describes it
as the intermediate size of the routed expert MLPs.

- **An expert** is a gated feed-forward block: it widens a token's vector to this width, applies
  SiLU gating, and narrows it back to hidden_size.
- **Every expert has this width,** so it multiplies through all experts in all layers.
- **Two designs:** a few wide experts (Mixtral, 8 of 14,336) or many narrow ones (Qwen3-30B-A3B,
  128 of 768).
- **The figure** compares them part by part, stored against used per token.

**Analogy:** the size of each workshop in a factory park; making every workshop bigger raises both
the park's floor space and the space any one job uses. The analogy shows the idea only.

## How It Works

**One width, applied to every expert.**

1. **Widen.** Each expert's gate and up projections map hidden_size to moe_intermediate_size; in
   Transformers they are stored together as one gate_up_proj per expert.
2. **Gate.** SiLU of the gate half multiplies the up half.
3. **Narrow.** The down projection maps the width back to hidden_size.
4. **Fine-grained experts.** DeepSeekMoE cuts each expert's width to 1/m and activates m times as
   many experts, keeping parameters and computation the same; the Qwen3 report follows this
   segmentation.

## Calculation

**One expert, then all of them.**

In words: three matrices of hidden_size × width per expert; all experts are stored, k are used.

$$
F = 3\,d\,f \qquad \text{stored} = L\,E\,F \qquad \text{active} = L\,k\,F
$$

| Symbol | Meaning |
| --- | --- |
| $d$ | hidden_size |
| $f$ | moe_intermediate_size (Mixtral: intermediate_size) |
| $L$ | num_hidden_layers |
| $E$, $k$ | number of experts, experts per token |

For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Qwen3-30B-A3B, from its config.json** (the numbers on its catalog page), beside Mixtral-8x7B:
hidden_size **2048**, width **768**, **128** experts, **8** per token, **48** layers.

1. **One expert:** 3 × 2048 × 768 = **4,718,592**.
2. **Stored experts:** 48 × 128 × 4,718,592 = **28,991,029,248**, 95.0% of its 30,532,122,624.
3. **Active experts:** 48 × 8 × 4,718,592 = **1,811,939,328** of its 3,353,032,704 active.
4. **Width ratio:** 768 ÷ 2048 = **0.375** of hidden_size.
5. **Mixtral's expert:** 3 × 4096 × 14,336 = **176,160,768**, a ratio of 3.5; its experts hold
   45,097,156,608 of its 46,702,792,704 parameters, 96.6%.

**What this means:** in both designs the experts are almost the whole model, and their width scales
the stored and the active count together.

## What To Watch

- **Which key holds the width.** moe_intermediate_size, or intermediate_size in Mixtral.
- **Dense layers.** Their width is intermediate_size; DeepSeek-V3's first three layers are dense.
- **Shared experts.** In DeepSeek-V3 the shared block is moe_intermediate_size × n_shared_experts
  wide.
