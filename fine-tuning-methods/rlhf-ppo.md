---
id: fine-tuning-method/rlhf-ppo
type: fine-tuning-method
slug: rlhf-ppo
schema_version: 1.0
title: "RLHF with PPO"
method_type: rlhf
memory_profile: high
summary: "Train a reward model on human preferences, then optimize the policy against it with PPO, held near a frozen reference by a KL penalty."
when_to_use: "Full reinforcement-learning alignment when you need on-policy optimization against a learned reward signal and can afford the multi-model training complexity — the InstructGPT/ChatGPT recipe."
related_hyperparameters: [hyperparameter/learning-rate]
tags: [fine-tuning, rlhf, ppo, alignment, reward-model]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2203.02155", title: "Training language models to follow instructions with human feedback (InstructGPT, Ouyang et al., 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1707.06347", title: "Proximal Policy Optimization Algorithms (Schulman et al., 2017)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1909.08593", title: "Fine-Tuning Language Models from Human Preferences (Ziegler et al., 2019)", trust: 0.9 }
---

## Definition
RLHF-PPO is the three-stage alignment pipeline introduced for InstructGPT (Ouyang et al., 2022).
**Stage 1 — SFT:** supervised fine-tune the base model on demonstration data. **Stage 2 — reward
model:** collect human rankings of model outputs and train a reward model `r_φ` (usually the SFT
model with a scalar head) to score responses via the Bradley-Terry preference loss. **Stage 3 —
PPO:** treat the language model as a policy and optimize it with Proximal Policy Optimization to
maximize the reward model's score on its own generations, while a **KL-divergence penalty** to a
frozen reference policy prevents it from drifting into text that games the reward model.

The per-token objective is roughly `reward = r_φ(response) − β · KL(π_θ ‖ π_ref)`. PPO's clipped
surrogate objective limits how far the policy moves per update, which is what keeps on-policy
RL stable on a model this large.

## What to look for
This is the most resource-intensive method: up to **four models** coexist during PPO — the trainable
policy, a frozen reference (for the KL term), the reward model, and a value/critic model — plus the
optimizer state for the trainable ones. The pivotal knob is the **KL penalty `β`**: too small and the
policy reward-hacks (produces high-scoring gibberish or sycophancy); too large and it barely moves
from the reference. Use a **very small policy learning rate** (see [[hyperparameter/learning-rate]]),
typically ~1e-6, and watch the KL divergence as the primary health metric — a runaway KL means the
policy is exploiting the reward model. Adaptive-KL controllers adjust `β` to hold KL near a target.
The pipeline is powerful but finicky: reward-model quality caps everything, and instability is common
— which is exactly why simpler alternatives like [[fine-tuning-method/dpo]] were developed.

## Calculation
No new architecture parameters — the policy has the base model's parameter count. The distinguishing
cost is the **number of models resident in memory**: policy (trainable, ~16 bytes/param with AdamW) +
frozen reference (~2 bytes/param) + reward model (~2 bytes/param) + critic (trainable). A rough
estimate for the policy alone mirrors full fine-tuning (`≈ 16 · P` bytes); the reference, reward, and
critic add roughly another `2·P + 2·P + 16·P`. Generation (rollouts) also adds inference memory and
is often the throughput bottleneck.

## Example
Align a 7B model. Stage 1: SFT on demonstrations. Stage 2: train a 7B reward model on human-ranked
pairs with the Bradley-Terry loss. Stage 3: PPO — for each prompt, sample a response from the policy,
score it with the reward model, compute `reward − β·KL(π_θ‖π_ref)` with an adaptive `β` targeting a
KL of ~6 nats, and take a clipped PPO update at learning rate `1e-6`. Four 7B-scale models sit in
memory, so this is a multi-GPU job. If reward climbs while generations degrade into repetitive
high-scoring patterns, the KL is too loose — tighten `β`. This is the recipe behind InstructGPT and
ChatGPT; [[fine-tuning-method/dpo]] and [[fine-tuning-method/orpo]] trade some of its on-policy power
for far simpler, more stable training.
