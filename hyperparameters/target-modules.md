---
id: hyperparameter/target-modules
type: hyperparameter
slug: target-modules
schema_version: 1.0
title: "LoRA Target Modules"
name: target_modules
context: training
priority: 7
importance: important
goals: [finetuning]
models: [llm, vision, audio, diffusion]
range: "attention-only (q,v) → all linear layers (q,k,v,o + gate,up,down)"
starting_value: "['q_proj','v_proj'] for light adaptation; all-linear for knowledge-heavy fine-tunes"
controls: "Which weight matrices receive LoRA adapters — determines where and how much the model can change."
if_increased: "Adapting more modules (adding FFN/MLP layers) gives broader capacity for new knowledge, at more parameters and memory."
if_decreased: "Fewer targeted modules train faster and lighter but limit how much behaviour can shift."
common_mistake: "Adapting only q,v and expecting the model to absorb new factual knowledge — knowledge lives largely in the FFN/MLP layers, which you must include for that."
tags: [finetuning, lora, peft, architecture]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2305.14314", title: "QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)", trust: 0.9 }
related: [hyperparameter/lora-r, hyperparameter/lora-alpha]
---

## Definition
`target_modules` lists which named linear layers of the transformer get LoRA adapters. Everything not
listed stays frozen at its pretrained value.

## What to look for
The choice trades capacity against cost. The original LoRA paper adapted only the attention query and
value projections (`q_proj`, `v_proj`) and matched full fine-tuning on many tasks. QLoRA found that
adapting **all linear layers** — attention `q,k,v,o` plus the FFN `gate_proj, up_proj, down_proj` — is
important for recovering quality, especially for absorbing knowledge, since the MLP blocks hold most of
the model's stored facts. Minimal `q,v` is a fine default for style/format; go all-linear when the task
needs real new knowledge or reasoning.

## Calculation
Total trainable parameters scale with the number of targeted modules. For each targeted layer of shape
$d_\text{out}\times d_\text{in}$, LoRA adds $r(d_\text{in}+d_\text{out})$ parameters; sum over every
targeted module across all layers:
$$\text{trainable} = \sum_{\text{module }m}\; r\,(d^{(m)}_\text{in} + d^{(m)}_\text{out}).$$

## Example
For a 7B model with `d_model=4096` and FFN hidden `d_ff=11008`, adapting only `q_proj,v_proj` at `r=16`
gives ~8.4M trainable params (see [[hyperparameter/lora-r]]). Switching to **all-linear** adds `k_proj,
o_proj` (each `4096×4096`) and the three FFN matrices (`gate/up: 4096→11008`, `down: 11008→4096`). Per
layer that is roughly $16\times[(4096{+}4096)\times4 + (4096{+}11008)\times3] \approx 1.25\text{M}$
params; across 32 layers ≈ **40M** trainable (~0.57% of 7B) — nearly 5× the q,v-only budget, and the
difference that lets the fine-tune actually learn new material.
