---
id: hyperparameter/lora-r
type: hyperparameter
slug: lora-r
schema_version: 1.0
title: "LoRA Rank (r)"
name: lora_r
context: training
priority: 5
importance: critical
goals: [finetuning]
models: [llm, vision, audio, diffusion]
range: "4 — 64 (8, 16, 32 most common)"
starting_value: "16 (general), 8 (light adaptation), 64 (hard tasks / more capacity)"
controls: "The rank of the low-rank update matrices — the capacity (number of trainable parameters) of each LoRA adapter."
if_increased: "More trainable capacity to fit complex tasks, but more memory, slower training, and higher overfitting risk on small data."
if_decreased: "Fewer parameters, faster and lighter, but may underfit tasks that need real behavioural change."
common_mistake: "Reaching for a large r (128+) by default — most fine-tunes saturate by r=16–32, and bigger just costs memory without accuracy gains."
tags: [finetuning, lora, peft, rank]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/peft/conceptual_guides/lora", title: "Hugging Face PEFT — LoRA conceptual guide", trust: 0.85 }
related: [hyperparameter/lora-alpha, hyperparameter/target-modules]
---

## Definition
LoRA freezes the pretrained weight matrix $W$ and learns a low-rank update $\Delta W = BA$, where the
rank $r$ is the shared inner dimension of $B \in \mathbb{R}^{d\times r}$ and $A \in \mathbb{R}^{r\times k}$.
`lora_r` sets that rank and therefore the adapter's capacity.

## What to look for
`r` is the single biggest capacity knob in a PEFT config. Small `r` (4–8) suits style/format adaptation;
mid `r` (16–32) suits most instruction fine-tuning; larger `r` (64) helps when the task demands new
knowledge or reasoning behaviour. Watch train-vs-eval loss: if train loss keeps dropping while eval
stalls, `r` is too high for your dataset size. It is almost always paired with `lora_alpha` (see
[[hyperparameter/lora-alpha]]) and a chosen set of `target_modules`.

## Calculation
Trainable parameters added per targeted linear layer of shape $d_\text{out}\times d_\text{in}$:
$$\text{params} = r\,(d_\text{in} + d_\text{out}).$$
When $d_\text{in}=d_\text{out}=d_\text{model}$ (e.g. attention q/v projections), this is
$2\,r\,d_\text{model}$ per module. Total trainable = that, summed over every targeted module and layer.

## Example
Take a 7B model with `d_model = 4096`, targeting `q_proj` and `v_proj` (both `4096×4096`) across 32
layers, at `r=16`. Per module: $2 \times 16 \times 4096 = 131{,}072$ params. Two modules × 32 layers =
64 modules, so total $\approx 64 \times 131{,}072 \approx 8.4\text{M}$ trainable parameters — about
**0.12%** of the 7B, yet enough to specialise the model. Doubling to `r=32` doubles that to ~16.8M with
usually marginal accuracy gain.
