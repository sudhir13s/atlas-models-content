---
id: hyperparameter/repetition-penalty
type: hyperparameter
slug: repetition-penalty
schema_version: 1.0
title: "Repetition Penalty"
name: repetition_penalty
context: inference
priority: 4
importance: important
goals: [inference]
models: [llm]
range: "1.0 — 1.3 (1.0 = no penalty)"
starting_value: "1.1 (chat / prose); 1.0 (code and structured output)"
controls: "Discounts the logits of tokens that have already appeared, discouraging loops and verbatim repetition."
if_increased: "Stronger push away from repeats, but too high distorts vocabulary and hurts fluency and correctness."
if_decreased: "Toward 1.0 removes the penalty — natural for code, but prose may fall into repetitive loops."
common_mistake: "Applying a repetition penalty to code generation — code legitimately repeats tokens (braces, keywords, variable names), and penalising them breaks syntax."
tags: [inference, sampling, decoding, repetition]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1909.05858", title: "CTRL: A Conditional Transformer Language Model (Keskar et al., 2019) — repetition penalty", trust: 0.85 }
  - { url: "https://huggingface.co/docs/transformers/main_classes/text_generation", title: "Hugging Face — GenerationConfig (repetition_penalty)", trust: 0.85 }
related: [hyperparameter/temperature, hyperparameter/top-p]
---

## Definition
Repetition penalty rescales the logit of any token that has already appeared in the prompt or generation
so far, lowering its probability of being chosen again. It targets the degenerate looping that greedy and
low-temperature decoding are prone to.

## What to look for
Values run just above 1.0: `1.1` is a good default for chat and prose; `1.0` (off) for code and
structured formats where repetition is legitimate. Above ~1.3 the penalty starts to distort the
distribution, dropping fluency and factual accuracy. It differs from the additive OpenAI-style
`frequency_penalty`/`presence_penalty`: repetition_penalty is **multiplicative on the logit** and applies
once a token has appeared, regardless of how many times.

## Calculation
For a token with logit $z_i$ that has already appeared, the penalised logit is (Keskar et al./HF):
$$z'_i = \begin{cases} z_i / \theta & \text{if } z_i > 0 \\ z_i \cdot \theta & \text{if } z_i \le 0 \end{cases},\qquad \theta = \texttt{repetition\_penalty} \ge 1.$$
Dividing a positive logit by $\theta>1$ shrinks it; multiplying a negative logit by $\theta$ makes it more
negative — both reduce the token's post-softmax probability.

## Example
A seen token has logit $z_i = 2.0$; with `repetition_penalty=1.1`, $z'_i = 2.0/1.1 = 1.818$. A seen token
with logit $-0.5$ becomes $-0.5 \times 1.1 = -0.55$. After softmax, both are less likely than before, so
the decoder is nudged toward fresh tokens — enough to break a "the the the" loop in prose. For a Python
completion, leave it at `1.0`: penalising the already-used `def`, `return`, or `    ` (indentation) would
produce invalid code.
