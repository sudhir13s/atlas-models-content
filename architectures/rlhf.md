---
id: architecture-era/rlhf
type: architecture-era
slug: rlhf
schema_version: 1.0
title: "RLHF & Instruction Alignment"
name: "Reinforcement Learning from Human Feedback"
era: post-transformer
era_range: "2022–2024"
year: 2022
family: llm
core_innovation: "Train a reward model on human preference comparisons, then fine-tune the LM with RL (PPO) plus a KL penalty to align it to human intent."
description: "The alignment recipe (InstructGPT) that turned raw next-token predictors into helpful, instruction-following assistants."
key_features: ["Supervised instruction fine-tuning", "Preference-trained reward model", "PPO policy optimization", "KL penalty to the reference model", "Helpfulness/harmlessness alignment"]
key_models: ["InstructGPT (2022)", "ChatGPT (2022)", "Llama-2-Chat (2023)"]
paper_title: "Training Language Models to Follow Instructions with Human Feedback (InstructGPT)"
paper_url: "https://arxiv.org/abs/2203.02155"
influenced_by: [architecture-era/gpt]
led_to: [architecture-era/reasoning-models]
tags: [rlhf, alignment, instruction-tuning, ppo, llm]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2203.02155", title: "Training Language Models to Follow Instructions with Human Feedback (Ouyang et al., 2022)", trust: 0.95 }
---

## What Changed
A base GPT model predicts likely next tokens — which is not the same as being *helpful*. Ask it a
question and it might continue with more questions, because that is a plausible continuation of the
text. RLHF closes the gap between "predict text" and "do what the user wants." The InstructGPT recipe
has three stages: (1) **supervised fine-tuning** on human-written demonstrations of good responses;
(2) train a **reward model** on human *comparisons* — people rank several model outputs, and the
reward model learns to score responses the way humans would; (3) **reinforcement learning** (PPO) that
updates the language model to maximise that reward, with a **KL-divergence penalty** anchoring it to
the original model so it improves preference without drifting into degenerate or off-distribution text.
The result — a striking jump in perceived helpfulness even at smaller sizes — is what made ChatGPT and
every modern assistant feel usable. RLHF descends from the GPT line and its preference-optimization
machinery is the foundation the later RL-trained reasoning models build on.

## Key Ideas
- **Preference learning** — humans compare outputs rather than writing perfect answers; ranking is
  cheaper and more reliable than absolute scoring, and trains the reward model.
- **Reward model as proxy** — a learned scorer stands in for expensive human judgement so RL can
  optimise against it at scale.
- **PPO with a KL leash** — policy-gradient RL maximises reward while a KL penalty to the frozen
  reference model prevents reward hacking and keeps generations fluent.
- **Alignment ≠ capability** — RLHF mostly elicits and directs abilities already present from
  pretraining; it makes models follow intent, not fundamentally smarter.
