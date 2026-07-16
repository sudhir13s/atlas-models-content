---
id: hyperparameter/temperature
type: hyperparameter
slug: temperature
schema_version: 1.0
title: "Temperature"
name: temperature
context: inference
priority: 1
importance: critical
goals: [inference]
models: [llm]
range: "0.0 — 2.0"
starting_value: "0.7 (general), 0.0–0.3 (factual), 1.0–1.2 (creative)"
controls: "Scales the logits before softmax, flattening or sharpening the next-token distribution."
if_increased: "More diverse, creative, and random output; above 1.0 amplifies unlikely tokens and hallucination."
if_decreased: "More focused and deterministic output; 0.0 is greedy decoding."
common_mistake: "Using a high temperature on factual tasks, which amplifies low-probability tokens and increases hallucination."
tags: [inference, sampling, decoding]
updated: 2026-07-16
sources:
  - { url: "https://huggingface.co/docs/transformers/generation_strategies", title: "Hugging Face — text generation strategies", trust: 0.85 }
---

## Definition
Temperature divides the logits before the softmax. Low values sharpen the distribution toward the
most likely tokens; high values flatten it, spreading probability across more candidates.

## What to look for
Match temperature to the task: factual / extraction / code → 0.0–0.3; general chat → ~0.7;
brainstorming / creative → 1.0–1.2. Tune temperature **or** `top_p`, not both to extremes at once —
they interact and jointly widening them produces incoherence.

## Calculation
For logits $z_i$, the sampled distribution is
$p_i = \dfrac{\exp(z_i / T)}{\sum_j \exp(z_j / T)}$. As $T \to 0$ this approaches the argmax (greedy);
as $T$ grows it approaches a uniform distribution over the vocabulary.

## Example
On a retrieval-augmented QA endpoint, set `temperature=0.2` for grounded, repeatable answers. For a
title brainstormer, `temperature=1.1` with `top_p=0.95`. Setting `temperature=1.5` on the QA
endpoint would surface unlikely tokens and produce confident-sounding wrong answers.
