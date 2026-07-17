---
id: hyperparameter/top-p
type: hyperparameter
slug: top-p
schema_version: 1.0
title: "Top-p (Nucleus Sampling)"
name: top_p
context: inference
priority: 2
importance: critical
goals: [inference]
models: [llm]
range: "0.0 — 1.0"
starting_value: "0.9 (general); 0.95 (creative); 1.0 disables it"
controls: "Restricts sampling to the smallest set of tokens whose cumulative probability reaches p — a dynamic candidate cutoff."
if_increased: "Toward 1.0 admits more of the tail — more diverse but riskier output; 1.0 means no nucleus cutoff at all."
if_decreased: "A tighter nucleus — safer, more focused output; very low p approaches greedy decoding."
common_mistake: "Cranking both temperature and top_p to extremes at once — they compound, producing incoherent text; tune one at a time."
tags: [inference, sampling, decoding, nucleus]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1904.09751", title: "The Curious Case of Neural Text Degeneration (Holtzman et al., 2020) — nucleus sampling", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/generation_strategies", title: "Hugging Face — text generation strategies", trust: 0.85 }
related: [hyperparameter/temperature, hyperparameter/top-k, hyperparameter/do-sample]
---

## Definition
Top-p, or nucleus sampling, keeps only the most probable tokens whose cumulative probability first
reaches the threshold $p$, renormalises over that set, and samples from it. The candidate set size adapts
to how confident the model is at each step.

## What to look for
`top_p=0.9` is a solid default; 0.95 for more creative output; `1.0` disables the cutoff (pure
temperature sampling). Unlike top-k, the nucleus is **dynamic**: on a confident step where one token
holds 0.92 probability, the nucleus is just that token; on an uncertain step it may include dozens. Tune
temperature **or** top_p as your primary randomness knob, not both aggressively (see
[[hyperparameter/temperature]]). Sampling only takes effect when `do_sample=true`.

## Calculation
Sort tokens by probability $p_1 \ge p_2 \ge \dots$. Choose the smallest $k^\*$ such that
$\sum_{i=1}^{k^\*} p_i \ge p$. Keep $\{1,\dots,k^\*\}$, zero the rest, renormalise:
$$p'_i = \frac{p_i}{\sum_{j=1}^{k^\*} p_j}\quad\text{for } i \le k^\*,\qquad p'_i = 0 \text{ otherwise}.$$

## Example
Next-token probabilities are `[0.5, 0.25, 0.13, 0.07, 0.03, 0.02]` with `top_p=0.9`. Cumulative sums:
0.5, 0.75, 0.88, **0.95**. The running total first reaches 0.9 at the 4th token (0.95 ≥ 0.9), so the
nucleus is the top 4; the last two tokens (0.03, 0.02) are dropped. Renormalising the kept four by their
sum 0.95 gives `[0.526, 0.263, 0.137, 0.074]`, and the sample is drawn from those. Lower `top_p=0.75`
and only the top 2 survive — far more focused.
