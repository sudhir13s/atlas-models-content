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

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 462" role="img" aria-label="A reasoning model's response as a token timeline: after the prompt it generates a long span of reasoning tokens, often hidden from the user but billed and counted as output, then a short final answer; more reasoning tokens cost latency and money and raise accuracy on multi-step problems, and the behaviour is trained with reinforcement learning on problems whose answers can be checked automatically.">
  <defs><marker id="reasoning-models-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="reasoning-models-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <rect class="d-box d-role-input" x="74.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="90.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="106.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="122.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="138.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="154.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="170.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-frame" x="195.5" y="65" width="501" height="27" rx="4"/>
  <rect class="d-box d-role-attention" x="201.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="217.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="233.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="249.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="265.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="281.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="297.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="313.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="329.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="345.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="361.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="377.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="393.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="409.5" y="72" width="13" height="13" rx="2"/>
  <text class="d-label" x="446" y="83" text-anchor="middle">⋯</text>
  <rect class="d-box d-role-attention" x="469.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="485.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="501.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="517.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="533.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="549.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="565.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="581.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="597.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="613.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="629.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="645.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="661.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="677.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="708.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="724.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="740.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="756.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="772.5" y="72" width="13" height="13" rx="2"/>
  <path class="d-line-strong" d="M74.5 64 V58 H183.5 V64"/>
  <text class="d-title" x="129" y="40" text-anchor="middle">Prompt</text>
  <text class="d-note" x="129" y="53" text-anchor="middle">input tokens</text>
  <path class="d-line-strong" d="M195.5 64 V58 H696.5 V64"/>
  <text class="d-title" x="446" y="40" text-anchor="middle">Reasoning span: hundreds to thousands of tokens</text>
  <text class="d-note" x="446" y="53" text-anchor="middle">in DeepSeek-R1 written between &lt;think&gt; and &lt;/think&gt;</text>
  <path class="d-line-strong" d="M708.5 64 V58 H785.5 V64"/>
  <text class="d-title" x="747" y="40" text-anchor="middle">Answer</text>
  <text class="d-note" x="747" y="53" text-anchor="middle">short, visible</text>
  <path class="d-line" d="M262.625 93 V101"/>
  <text class="d-note" x="262.625" y="113" text-anchor="middle">works through steps</text>
  <path class="d-line" d="M384.875 93 V101"/>
  <text class="d-note" x="384.875" y="113" text-anchor="middle">re-checks a step</text>
  <path class="d-line" d="M507.125 93 V101"/>
  <text class="d-note" x="507.125" y="113" text-anchor="middle">“wait…” re-evaluates</text>
  <path class="d-line" d="M629.375 93 V101"/>
  <text class="d-note" x="629.375" y="113" text-anchor="middle">tries another approach</text>
  <path class="d-line-strong" d="M195.5 125 V131 H785.5 V125"/>
  <text class="d-label" x="490.5" y="147" text-anchor="middle">Reasoning + answer are both output: billed as output tokens, counted against the output limit (OpenAI API)</text>
  <text class="d-note" x="490.5" y="161" text-anchor="middle">the raw reasoning is often hidden: o1 shows the user only a summary (DeepSeek-R1 does show it)</text>
  <path class="d-line-strong" d="M74.5 192 H785.5" marker-end="url(#reasoning-models-arrow)"/>
  <text class="d-note" x="74.5" y="208" text-anchor="start">generated one token after another: every reasoning token adds decoding time before the answer starts</text>
  <path class="d-line" d="M16 226 H844" opacity="0.6"/>
  <text class="d-title" x="224" y="248" text-anchor="middle">More thinking buys accuracy (schematic)</text>
  <path class="d-line-strong" d="M74 264 V394 H374"/>
  <text class="d-label" x="0" y="0" text-anchor="middle" transform="translate(60 329) rotate(-90)">accuracy</text>
  <text class="d-label" x="224" y="412" text-anchor="middle">reasoning tokens spent per problem (log scale)</text>
  <path class="d-line-accent" d="M82.0 378.4 L91.5 370.2 L100.9 362.7 L110.4 355.8 L119.9 349.5 L129.3 343.7 L138.8 338.3 L148.3 333.5 L157.7 329.0 L167.2 324.9 L176.7 321.1 L186.1 317.7 L195.6 314.5 L205.1 311.6 L214.5 309.0 L224.0 306.5 L233.5 304.3 L242.9 302.2 L252.4 300.4 L261.9 298.6 L271.3 297.1 L280.8 295.6 L290.3 294.3 L299.7 293.1 L309.2 291.9 L318.7 290.9 L328.1 290.0 L337.6 289.1 L347.1 288.3 L356.5 287.6 L366.0 286.9"/>
  <text class="d-note" x="74" y="430" text-anchor="start">cost rises too: more latency, more billed tokens</text>
  <path class="d-line-strong" d="M310 426 H374" marker-end="url(#reasoning-models-arrow)"/>
  <text class="d-note" x="224" y="356" text-anchor="start">reported on multi-step maths</text>
  <text class="d-note" x="224" y="369" text-anchor="start">and coding benchmarks</text>
  <text class="d-title" x="625" y="248" text-anchor="middle">Trained by RL on checkable answers (DeepSeek-R1)</text>
  <path class="d-line-accent" d="M727.8 294.7 A160 80 0 0 1 774.4 327.3" marker-end="url(#reasoning-models-arrow-accent)"/>
  <path class="d-line-accent" d="M774.4 384.7 A160 80 0 0 1 732.1 415.5" marker-end="url(#reasoning-models-arrow-accent)"/>
  <path class="d-line-accent" d="M517.9 415.5 A160 80 0 0 1 475.6 384.7" marker-end="url(#reasoning-models-arrow-accent)"/>
  <path class="d-line-accent" d="M475.6 327.3 A160 80 0 0 1 522.2 294.7" marker-end="url(#reasoning-models-arrow-accent)"/>
  <rect class="d-box d-role-input" x="533" y="259" width="184" height="34" rx="5"/>
  <text class="d-label" x="625" y="273" text-anchor="middle">problem with a checkable answer</text>
  <text class="d-note" x="625" y="287" text-anchor="middle">maths result · code with tests</text>
  <rect class="d-box d-role-attention" x="717" y="339" width="136" height="34" rx="5"/>
  <text class="d-label" x="785" y="353" text-anchor="middle">model answers</text>
  <text class="d-note" x="785" y="367" text-anchor="middle">&lt;think&gt;…&lt;/think&gt; answer</text>
  <rect class="d-box d-role-router" x="532" y="419" width="186" height="34" rx="5"/>
  <text class="d-label" x="625" y="433" text-anchor="middle">rule-based check</text>
  <text class="d-note" x="625" y="447" text-anchor="middle">answer correct? format followed?</text>
  <rect class="d-box d-role-feedforward" x="409" y="339" width="112" height="34" rx="5"/>
  <text class="d-label" x="465" y="353" text-anchor="middle">reward → RL update</text>
  <text class="d-note" x="465" y="367" text-anchor="middle">of the model (GRPO)</text>
  <text class="d-note" x="625" y="352" text-anchor="middle">the reward looks only at the</text>
  <text class="d-note" x="625" y="366" text-anchor="middle">final answer and the format</text>
  </svg>
diagram_caption: "A reasoning model writes a long, often hidden reasoning span before a short answer; all of it is billed output, so more thinking costs latency and money for accuracy (schematic). RL on automatically checkable answers trains it."
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
  - { url: "https://openai.com/index/learning-to-reason-with-llms/", title: "Learning to reason with LLMs (OpenAI, 2024): accuracy against test-time compute; the raw chain of thought is shown only as a summary", trust: 0.85 }
  - { url: "https://developers.openai.com/api/docs/guides/reasoning", title: "Reasoning models guide (OpenAI API): reasoning tokens are not visible, are billed as output and count against the output limit", trust: 0.85 }
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
