---
id: hyperparameter/min-new-tokens
type: hyperparameter
slug: min-new-tokens
schema_version: 1.0
title: "Min New Tokens"
name: min_new_tokens
context: inference
priority: 8
importance: optional
goals: [inference]
models: [llm]
range: "0 — a few hundred (well below max_new_tokens)"
starting_value: "0 (unset); set a small floor (e.g. 32) only when outputs stop too early"
controls: "A floor on generation length — the end-of-sequence token is suppressed until this many new tokens are produced."
if_increased: "Forces longer output, useful when the model stops prematurely, but padding past the natural answer adds filler."
if_decreased: "Toward 0 lets the model stop whenever it wants — natural length, but risks over-short replies."
common_mistake: "Setting min_new_tokens close to (or above) max_new_tokens, which forces near-max-length output on every call and produces rambling, padded answers."
tags: [inference, generation, length]
updated: 2026-07-17
sources:
  - { url: "https://huggingface.co/docs/transformers/main_classes/text_generation", title: "Hugging Face — GenerationConfig (min_new_tokens)", trust: 0.85 }
  - { url: "https://huggingface.co/docs/transformers/generation_strategies", title: "Hugging Face — text generation strategies", trust: 0.85 }
related: [hyperparameter/max-new-tokens, hyperparameter/num-beams]
---

## Definition
`min_new_tokens` sets a minimum number of tokens the model must generate before it is allowed to stop. It
works by masking the end-of-sequence (EOS) token's logit to $-\infty$ until the floor is reached, so
generation cannot terminate early.

## What to look for
It defaults to 0 (no floor) and is a targeted fix, not a routine knob. Reach for it only when a model
ends completions too soon — emitting EOS after a couple of tokens, or returning an empty string. Keep it
well below `max_new_tokens` so it nudges rather than forces length. Like `max_new_tokens`, it counts only
new (completion) tokens, not the prompt. Prefer the `*_new_tokens` pair over the prompt-inclusive
`min_length`/`max_length`.

## Calculation
While the number of generated tokens $t$ is below the floor, the EOS logit is suppressed:
$$z_\text{EOS} \leftarrow -\infty \quad\text{for all } t < \texttt{min\_new\_tokens},$$
so $P(\text{EOS}) = 0$ until the floor; afterward the EOS token competes normally. The realised length
therefore satisfies $\texttt{min\_new\_tokens} \le \text{generated} \le \texttt{max\_new\_tokens}$.

## Example
A summarizer keeps returning one-line outputs when you want at least a short paragraph. Set
`min_new_tokens=40` with `max_new_tokens=256`: EOS is blocked for the first 40 tokens, so the model must
produce at least that much before it may stop, and it still ends naturally anywhere from 40 up to 256
tokens. Setting `min_new_tokens=250` instead would force nearly the full 256-token budget every time,
padding short answers with filler.
