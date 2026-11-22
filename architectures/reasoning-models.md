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
core_innovation: "A long chain of thought, trained with large-scale reinforcement learning, generated before the answer, so that more inference-time compute buys accuracy."
description: "Models such as OpenAI o1 and DeepSeek-R1, trained with reinforcement learning to reason at length before answering; accuracy improves with more test-time compute."
key_features: ["Long chain of thought before the answer", "Trained with large-scale reinforcement learning", "Accuracy improves with more test-time compute", "Self-correction within the chain of thought"]
key_models: ["OpenAI o1 (2024)", "DeepSeek-R1 (2025)", "o3 (2025)"]
paper_title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning"
paper_url: "https://arxiv.org/abs/2501.12948"
influenced_by: [architecture-era/rlhf, architecture-era/mixture-of-experts]
led_to: []

eli5: "A standard chatbot starts writing its answer straight away. A reasoning model first writes out a long train of thought, checking its own steps and trying another approach when one fails, and only then answers. Letting it think longer tends to give more correct answers on hard problems."

analogy:
  title: "The student who shows their work"
  body: "Two students take a hard maths exam. The first writes an answer straight away. The second fills pages with working, spots a mistake, crosses it out, tries another approach, and only then circles a final answer. Reasoning models are the second student, and they were coached by checking whether the final answer is right rather than by copying worked solutions."

narrative:
  motivation: "Chain-of-thought prompting (Wei et al., 2022) improved reasoning by showing models worked examples. The DeepSeek-R1 authors note that this success depends heavily on human-annotated demonstrations and is still insufficient for complex problems."
  approach: "Train the model with large-scale reinforcement learning to produce a long chain of thought before its answer. DeepSeek-R1 rewards the model with rule-based checks of answer accuracy and output format on problems whose answers can be verified, without human-labelled reasoning trajectories."
  solution: "During reinforcement learning the model's chains of thought grow longer, and patterns such as self-reflection, verification and changing strategy appear. OpenAI reports that o1 learns to recognise and correct its mistakes and to try a different approach when one fails."
  outcome: "OpenAI reports that o1's performance improves with more reinforcement learning (train-time compute) and with more time spent thinking (test-time compute). DeepSeek-R1's authors report strong results on mathematics, coding competitions and STEM tasks, and distilled its reasoning into smaller public models."

build_steps:
  - { title: "1. Start from a pretrained base model", detail: "DeepSeek-R1-Zero applies reinforcement learning directly to the DeepSeek-V3 base model." }
  - { title: "2. Pick tasks with checkable answers", detail: "Use problems whose correctness can be verified automatically, such as maths with known answers or code that must pass tests." }
  - { title: "3. Reward correct final answers", detail: "Reward the final answer's accuracy plus the output format (reasoning inside <think> tags), with rule-based checks rather than a neural reward model, as DeepSeek-R1 does." }
  - { title: "4. Let long reasoning develop", detail: "Under this reward, DeepSeek-R1-Zero's thinking time grows steadily through training, with self-reflection and verification in its chains of thought." }
  - { title: "5. Optionally add cold-start data and distil", detail: "DeepSeek-R1 first fine-tunes on a small set of long chain-of-thought examples before reinforcement learning, and its reasoning is distilled into smaller models." }
  - { title: "6. Scale compute at inference", detail: "At test time, allow more thinking on hard problems; OpenAI reports o1 and o3 improve the longer they are allowed to think." }

key_equation:
  latex: "P(a \\mid q) = \\sum_{r} P(r \\mid q)\\, P(a \\mid q, r)"
  plain: "The probability of an answer is a sum over possible reasoning chains: how likely the model is to write each chain, times how likely that chain leads to the answer. Rewarding correct final answers pushes probability toward chains that reach them."
  terms:
    - { symbol: "q", meaning: "the question or problem prompt" }
    - { symbol: "a", meaning: "the final answer the model commits to" }
    - { symbol: "r", meaning: "a reasoning chain (chain of thought) — the intermediate steps the model writes before answering" }
    - { symbol: "P(r | q)", meaning: "how likely the model is to generate reasoning chain r for question q" }
    - { symbol: "P(a | q, r)", meaning: "how likely that chain leads to answer a — reasoning that reaches the correct answer is what training rewards" }

strengths:
  - "Reported gains on mathematics, coding competitions and STEM tasks."
  - "More thinking at test time improves results without retraining, per OpenAI's o1 and o3 reports."
  - "DeepSeek-R1's rewards are rule-based checks, so the reasoning trajectories need no human labels."
  - "Reasoning can be distilled into smaller models."

weaknesses:
  - "Every reasoning token is generated before the answer and, in the OpenAI API, billed as an output token."
  - "Rule-based rewards need problems whose answers can be checked automatically."
  - "For o1, users see only a model-generated summary of the chain of thought, not the raw reasoning."

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
  <text class="d-title" x="446" y="40" text-anchor="middle">Reasoning span: a long chain of thought</text>
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
underlying_model_note: "DeepSeek-R1 is a language model underneath: its makers took DeepSeek-V3-Base and trained it further with reinforcement learning, so the reasoning comes from the training, not from a different network."
notable_models:
  - { name: "OpenAI o1", year: 2024, note: "Trained with large-scale reinforcement learning; OpenAI reports performance improving with both train-time and test-time compute." }
  - { name: "DeepSeek-R1", year: 2025, note: "Cold-start fine-tuning on long chain-of-thought data, then reinforcement learning with rule-based rewards; smaller distilled models were released." }
  - { name: "DeepSeek-R1-Zero", year: 2025, note: "Reinforcement learning applied directly to the DeepSeek-V3 base model, without human-labelled reasoning trajectories." }
  - { name: "o3", year: 2025, note: "OpenAI reports an additional order of magnitude in training compute and inference-time reasoning over o1." }

resources:
  - { kind: paper, title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (DeepSeek-AI, 2025)", url: "https://arxiv.org/abs/2501.12948" }
  - { kind: blog, title: "Learning to Reason with LLMs (OpenAI o1)", url: "https://openai.com/index/learning-to-reason-with-llms/" }
  - { kind: paper, title: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (Wei et al., 2022)", url: "https://arxiv.org/abs/2201.11903" }
  - { kind: blog, title: "Why We Think — reasoning and test-time compute (Lilian Weng)", url: "https://lilianweng.github.io/posts/2025-05-01-thinking/" }

learn_first:
  - { label: "RLHF & preference-optimization machinery", href: "/models/architecture/rlhf", tag: "Concept" }
  - { label: "Mixture of Experts (DeepSeek-R1's base model)", href: "/models/architecture/mixture-of-experts", tag: "Architecture" }
  - { label: "Chain-of-thought prompting", href: "/learn/concepts/chain-of-thought", tag: "Concept" }

tags: [reasoning, chain-of-thought, rl, test-time-compute, llm]
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/2501.12948", title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (DeepSeek-AI, 2025)", trust: 0.95 }
  - { url: "https://openai.com/index/introducing-o3-and-o4-mini/", title: "Introducing OpenAI o3 and o4-mini (OpenAI, 16 April 2025)", trust: 0.85 }
  - { url: "https://arxiv.org/abs/2201.11903", title: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (Wei et al., 2022)", trust: 0.9 }
  - { url: "https://openai.com/index/learning-to-reason-with-llms/", title: "Learning to reason with LLMs (OpenAI, 2024): accuracy against test-time compute; the raw chain of thought is shown only as a summary", trust: 0.85 }
  - { url: "https://developers.openai.com/api/docs/guides/reasoning", title: "Reasoning models guide (OpenAI API): reasoning tokens are not visible, are billed as output and count against the output limit", trust: 0.85 }
---

## What Changed
Reasoning models are trained with **large-scale reinforcement learning** to write a long **chain of
thought** before their answer.

- OpenAI reports that o1's performance improves with more reinforcement learning and with more time
  spent thinking at test time (**inference-time scaling**).
- DeepSeek-R1 uses rule-based rewards for answer accuracy and format on checkable problems; its
  authors report self-reflection and verification appearing during training.
- Smaller models can learn the behaviour by distillation.

## Key Ideas
- **Long chain of thought**: the model works through, checks and revises intermediate steps before
  answering.
- **Reinforcement learning on checkable answers**: in DeepSeek-R1, correctness and format checks
  provide the reward, with no human-labelled reasoning trajectories.
- **Inference-time scaling**: more test-time compute (more reasoning tokens) improves accuracy, a
  lever separate from parameters and training data.
- **Relation to RLHF**: both fine-tune with reinforcement learning; DeepSeek-R1 replaces a learned
  preference model with rule-based rewards for reasoning tasks.
