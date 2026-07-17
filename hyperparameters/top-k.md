---
id: hyperparameter/top-k
type: hyperparameter
slug: top-k
schema_version: 1.0
title: "Top-k Sampling"
name: top_k
context: inference
priority: 5
importance: important
goals: [inference]
models: [llm]
range: "0 — 100 (0 disables the cap)"
starting_value: "50 (when used); often 0, letting top_p do the cutoff"
controls: "Restricts sampling to the k highest-probability tokens — a fixed-size candidate cap."
if_increased: "A larger candidate pool — more diverse output; very large k approaches unfiltered sampling."
if_decreased: "A tighter pool — more focused output; k=1 is greedy decoding."
common_mistake: "Using a fixed top_k as the main filter — its cutoff is blind to confidence (it keeps k tokens whether the top one is at 0.99 or 0.05); top_p adapts and is usually preferred."
tags: [inference, sampling, decoding]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1805.04833", title: "Hierarchical Neural Story Generation (Fan et al., 2018) — top-k sampling", trust: 0.85 }
  - { url: "https://huggingface.co/docs/transformers/generation_strategies", title: "Hugging Face — text generation strategies", trust: 0.85 }
related: [hyperparameter/top-p, hyperparameter/temperature, hyperparameter/do-sample]
---

## Definition
Top-k sampling keeps only the $k$ tokens with the highest probability, zeros the rest, renormalises, and
samples from that fixed-size set. It was the original "truncate the tail" decoding fix.

## What to look for
`top_k=50` is a common value; `top_k=0` disables it (no cap). Its limitation versus nucleus sampling is
that $k$ is **static**: it keeps the same number of candidates regardless of the model's confidence, so
on a very peaked step it admits low-probability tokens, and on a flat step it may cut off good ones. Many
modern stacks set `top_k=0` and rely on `top_p` (see [[hyperparameter/top-p]]); the two can also be
combined (apply top_k first, then top_p on the survivors). Only active when `do_sample=true`.

## Calculation
Sort tokens by probability $p_1 \ge p_2 \ge \dots$. Keep the top $k$ and renormalise:
$$p'_i = \frac{p_i}{\sum_{j=1}^{k} p_j}\quad\text{for } i \le k,\qquad p'_i = 0 \text{ otherwise}.$$

## Example
Probabilities `[0.5, 0.25, 0.13, 0.07, 0.03, 0.02]` with `top_k=3`: keep the top 3 (0.5, 0.25, 0.13),
drop the rest. Renormalise by their sum 0.88 → `[0.568, 0.284, 0.148]`, and sample from those. Notice
this cut happens the same way whether the top token was 0.5 or 0.95 — that confidence-blindness is why
`top_p` (which would keep just the one 0.95 token) is often preferred as the primary filter.
