---
id: architecture-era/reasoning-models
type: architecture-era
slug: reasoning-models
schema_version: 1.0
title: "Reasoning Models (Inference-Time Scaling)"
name: "Reasoning Models"
era: post-transformer
era_range: "2024–2026"
year: 2024
family: llm
core_innovation: "RL-trained long chain-of-thought that spends more inference-time compute to reason before answering — scaling test-time, not just training."
description: "The o1/R1 generation that learns to think in long internal chains, trading inference compute for accuracy on hard problems."
key_features: ["Long chain-of-thought generation", "RL on verifiable rewards", "Inference-time (test-time) compute scaling", "Self-checking and backtracking", "Emergent reasoning from RL"]
key_models: ["OpenAI o1 (2024)", "DeepSeek-R1 (2025)", "o3 (2025)"]
paper_title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning"
paper_url: "https://arxiv.org/abs/2501.12948"
influenced_by: [architecture-era/rlhf, architecture-era/mixture-of-experts]
led_to: []
tags: [reasoning, chain-of-thought, rl, test-time-compute, llm]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/2501.12948", title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (DeepSeek-AI, 2025)", trust: 0.95 }
---

## What Changed
Standard LLMs answer in roughly constant compute: whatever the question, they emit tokens at the same
per-token cost, so a hard maths or coding problem gets no more "thinking" than an easy one. Reasoning
models change the scaling axis. They are trained — largely with **reinforcement learning on verifiable
rewards** (problems where an answer can be automatically checked correct) — to generate a long internal
**chain of thought** before committing to an answer: working through steps, checking themselves, and
backtracking when a path fails. DeepSeek-R1 notably showed that this reasoning behaviour can *emerge*
from RL with minimal supervised hand-holding, given a correctness signal. The striking result is
**inference-time scaling**: accuracy on hard reasoning benchmarks keeps improving as you let the model
spend more tokens (more compute) thinking at test time — a new lever orthogonal to making the model
bigger or training it longer. These models build on the RLHF preference-optimization machinery
(swapping human preference for verifiable correctness) and typically run on efficient MoE backbones,
placing them at the current frontier of the timeline.

## Key Ideas
- **Long chain-of-thought** — the model reasons at length internally before answering, exploring and
  checking intermediate steps rather than replying in one shot.
- **RL on verifiable rewards** — for tasks with a checkable answer (math, code), correctness provides a
  clean reward signal, and reasoning strategies emerge from optimizing it.
- **Inference-time scaling** — spending more test-time compute (more reasoning tokens) buys more
  accuracy, a scaling knob separate from parameters and training data.
- **Builds on RLHF + MoE** — it reuses preference-style RL optimization with an automated reward, and
  commonly rides on sparse MoE models for efficient serving of long reasoning traces.
