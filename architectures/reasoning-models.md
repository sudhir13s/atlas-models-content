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

eli5: "Normal chatbots blurt out an answer at the same speed whether the question is easy or a hard math problem. Reasoning models learn to think first — writing out a long private train of thought, checking their own steps, and backing up when they're wrong — before answering. The longer you let them think, the more they get right."

analogy:
  title: "The student who shows their work"
  body: "Two students take a hard math exam. The first writes the answer instantly from gut feel and often gets it wrong. The second scribbles pages of working — trying an approach, spotting a mistake, crossing it out, trying another — and only then circles a final answer. Reasoning models are the second student, and they were trained the way you'd coach one: not by handing them worked solutions, but by checking whether their final answer is right and rewarding the trains of thought that lead to correct answers. Give them a bigger scratchpad (more thinking tokens) and their scores keep climbing."

narrative:
  motivation: "Standard LLMs answer in roughly constant compute: whatever the question, they emit tokens at the same per-token cost, so a hard math or coding problem gets no more 'thinking' than an easy one. Prompting a model to reason step by step helped, but the model wasn't actually trained to reason well — to plan, verify, and recover from wrong turns."
  approach: "Change the scaling axis from bigger-model to more-thinking. Train the model — largely with reinforcement learning on verifiable rewards, problems whose answers can be automatically checked — to generate a long internal chain of thought before committing. The reward comes from getting the final answer right, so the model is free to discover whatever reasoning strategy works."
  solution: "The model learns to produce long reasoning traces: working through steps, checking itself, and backtracking when a path fails. DeepSeek-R1 notably showed this behavior can emerge from RL with minimal supervised hand-holding, given only a correctness signal — the model teaches itself to reason. Because the reward is automatic (a checker, not a human), the loop scales cheaply compared to human-preference RLHF."
  outcome: "The striking result is inference-time scaling: accuracy on hard reasoning benchmarks keeps improving as you let the model spend more tokens — more compute — thinking at test time. That's a new lever, orthogonal to making the model bigger or training it longer. These models reuse the RLHF preference-optimization machinery (swapping human preference for verifiable correctness) and typically run on efficient MoE backbones, placing them at the current frontier."

build_steps:
  - { title: "1. Start from a strong base model", detail: "Begin with a capable pretrained (and often instruction-tuned) LLM — usually on an efficient MoE backbone to make long generations affordable to serve." }
  - { title: "2. Pick tasks with checkable answers", detail: "Focus on problems where correctness can be verified automatically — math with known answers, code that must pass tests. This gives a clean, cheap reward signal." }
  - { title: "3. Reward correct final answers", detail: "Use reinforcement learning where the reward depends on whether the final answer is right (plus light format rewards), not on imitating a human-written solution." }
  - { title: "4. Let long reasoning emerge", detail: "Optimizing for correctness, the model learns on its own to generate long chains of thought — planning, self-checking, and backtracking — because those traces earn more reward." }
  - { title: "5. Optionally distill and polish", detail: "Distill the reasoning behavior into smaller models, and add a light supervised or preference stage to make the traces readable and the model well-behaved." }
  - { title: "6. Scale compute at inference", detail: "At test time, spend more thinking tokens (or sample several chains and vote) on hard problems. Accuracy rises with the compute you're willing to spend." }

key_equation:
  latex: "P(a \\mid q) = \\sum_{r} P(r \\mid q)\\, P(a \\mid q, r)"
  plain: "The probability of a correct answer is a sum over all possible reasoning chains: how likely the model is to think of each chain, times how likely that chain leads to the right answer. Training pushes probability toward reasoning chains r that produce correct answers; at inference, sampling more or longer chains explores more of this sum, which is why spending more thinking-time compute buys more accuracy."
  terms:
    - { symbol: "q", meaning: "the question or problem prompt" }
    - { symbol: "a", meaning: "the final answer the model commits to" }
    - { symbol: "r", meaning: "a reasoning chain (chain of thought) — the intermediate steps the model writes before answering" }
    - { symbol: "P(r | q)", meaning: "how likely the model is to generate reasoning chain r for question q" }
    - { symbol: "P(a | q, r)", meaning: "how likely that chain leads to answer a — reasoning that reaches the correct answer is what training rewards" }

strengths:
  - "Large accuracy gains on hard math, coding, and logic where step-by-step reasoning matters."
  - "Inference-time scaling: a new knob — spend more thinking tokens to get better answers, no retraining needed."
  - "RL on verifiable rewards is automatic and cheap to scale — no expensive human labels in the core loop."
  - "Reasoning behavior can emerge from RL alone, and can be distilled into smaller, cheaper models."

weaknesses:
  - "Long chains of thought make each answer slower and more expensive to generate."
  - "Verifiable rewards mostly cover math/code; open-ended tasks lack a clean automatic correctness signal."
  - "Reasoning can overthink easy questions, and the visible trace may not faithfully reflect the real computation."
  - "Reward hacking risk — the model can learn to satisfy the checker without genuinely solving the problem."

diagram: |
  flowchart TD
    Q["Hard problem"] --> COT["Generate long chain of thought"]
    COT --> STEP["Work through a step"]
    STEP --> CHECK{"Step looks right?"}
    CHECK -->|"no"| BACK["Backtrack, try another path"]
    BACK --> STEP
    CHECK -->|"yes"| MORE{"Done reasoning?"}
    MORE -->|"no"| STEP
    MORE -->|"yes"| ANS["Commit to final answer"]
    ANS --> VER["Verifier checks correctness"]
    VER -->|"reward"| RL["RL updates the model"]
diagram_caption: "The model reasons in a long internal chain — stepping, checking, and backtracking — then commits an answer. A verifier's correctness signal is the reward that trains the reasoning; more thinking at test time buys more accuracy."

notable_models:
  - { name: "OpenAI o1", year: 2024, note: "First mainstream reasoning model — demonstrated inference-time scaling, with accuracy rising as it spent more thinking compute." }
  - { name: "DeepSeek-R1", year: 2025, note: "Showed strong reasoning can emerge from RL on verifiable rewards with minimal supervised data — and released the recipe openly." }
  - { name: "DeepSeek-R1-Zero", year: 2025, note: "Pure-RL variant with no supervised fine-tuning first, proving reasoning behavior can bootstrap from a correctness signal alone." }
  - { name: "o3", year: 2025, note: "Pushed inference-time scaling further, reaching new highs on hard reasoning and coding benchmarks." }

resources:
  - { kind: paper, title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (DeepSeek-AI, 2025)", url: "https://arxiv.org/abs/2501.12948" }
  - { kind: blog, title: "Learning to Reason with LLMs (OpenAI o1)", url: "https://openai.com/index/learning-to-reason-with-llms/" }
  - { kind: paper, title: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (Wei et al., 2022)", url: "https://arxiv.org/abs/2201.11903" }
  - { kind: blog, title: "Why We Think — reasoning and test-time compute (Lilian Weng)", url: "https://lilianweng.github.io/posts/2025-05-01-thinking/" }

learn_first:
  - { label: "RLHF & preference-optimization machinery", href: "/models/architecture/rlhf", tag: "Concept" }
  - { label: "Mixture of Experts (the usual efficient backbone)", href: "/models/architecture/mixture-of-experts", tag: "Architecture" }
  - { label: "Chain-of-thought prompting", href: "/learn/concepts/chain-of-thought", tag: "Concept" }

tags: [reasoning, chain-of-thought, rl, test-time-compute, llm]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2501.12948", title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (DeepSeek-AI, 2025)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2201.11903", title: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (Wei et al., 2022)", trust: 0.9 }
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
