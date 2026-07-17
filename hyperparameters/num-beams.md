---
id: hyperparameter/num-beams
type: hyperparameter
slug: num-beams
schema_version: 1.0
title: "Number of Beams"
name: num_beams
context: inference
priority: 7
importance: optional
goals: [inference]
models: [llm]
range: "1 — 8 (1 = no beam search)"
starting_value: "1 (chat/open-ended); 4–5 (translation, summarization)"
controls: "How many candidate sequences beam search keeps in parallel, exploring for a higher-probability overall output."
if_increased: "Explores more hypotheses for a higher-likelihood sequence, but multiplies compute/memory and tends toward safe, generic, repetitive text."
if_decreased: "Cheaper and more diverse; num_beams=1 reduces to greedy or plain sampling."
common_mistake: "Using beam search for open-ended chat or creative writing — it optimises for high-probability sequences, which come out bland and repetitive; beams suit constrained tasks like translation."
tags: [inference, decoding, beam-search]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1904.09751", title: "The Curious Case of Neural Text Degeneration (Holtzman et al., 2020)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/generation_strategies", title: "Hugging Face — text generation strategies", trust: 0.85 }
related: [hyperparameter/do-sample, hyperparameter/max-new-tokens, hyperparameter/min-new-tokens]
---

## Definition
Beam search keeps the `num_beams` most probable partial sequences at every step, expanding each and
retaining the best overall, to approximate the highest-probability *full* sequence rather than making a
locally greedy choice at each token.

## What to look for
`num_beams=1` is greedy/sampling (no beam search). Values of 4–5 are typical for tasks with a
constrained "correct" output — machine translation, summarization, grammatical correction — where
maximising sequence likelihood aligns with quality. For **open-ended** generation (chat, stories), beam
search underperforms sampling: it produces bland, repetitive, high-probability text (the degeneration
Holtzman et al. document). Beam search usually pairs with `length_penalty` and `early_stopping`, and is
distinct from sampling (`do_sample`).

## Calculation
Beam search approximately maximises the sequence log-probability
$$\log P(y_{1:T}\mid x) = \sum_{t=1}^{T} \log P(y_t \mid y_{<t}, x),$$
keeping the top-$B$ (=`num_beams`) partial sequences by this score at each step. Cost and memory scale
roughly linearly in $B$: $\mathcal{O}(B \cdot T)$ forward evaluations versus $\mathcal{O}(T)$ for greedy.

## Example
Translating a sentence with `num_beams=5`: at each step the decoder keeps the 5 highest-scoring partial
translations, so a locally second-best word that yields a better *overall* sentence can still win — often
a cleaner translation than greedy's step-by-step commitment. The cost is ~5× the compute. Apply the same
`num_beams=5` to a creative story prompt and the output turns generic and loop-prone; there you want
`num_beams=1` with `do_sample=true`, `temperature≈0.9`.
