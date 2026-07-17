---
id: hyperparameter/max-new-tokens
type: hyperparameter
slug: max-new-tokens
schema_version: 1.0
title: "Max New Tokens"
name: max_new_tokens
context: inference
priority: 3
importance: critical
goals: [inference]
models: [llm]
range: "16 — 8192+ (bounded by context window)"
starting_value: "2–3× the expected answer length (e.g. 512 for chat, 2048 for long generation)"
controls: "The hard cap on how many tokens the model may generate before stopping."
if_increased: "Room for longer answers, but higher latency, cost, and risk of rambling past the useful answer."
if_decreased: "Faster and cheaper, but a too-low cap truncates the answer mid-sentence."
common_mistake: "Setting it to the full context window (e.g. 128k) 'to be safe' — you pay latency for capacity you never use, and it invites run-on output; size it to the task."
tags: [inference, generation, length]
updated: 2026-07-17
sources:
  - { url: "https://huggingface.co/docs/transformers/main_classes/text_generation", title: "Hugging Face — GenerationConfig (max_new_tokens)", trust: 0.85 }
  - { url: "https://huggingface.co/docs/transformers/generation_strategies", title: "Hugging Face — text generation strategies", trust: 0.85 }
related: [hyperparameter/min-new-tokens, hyperparameter/num-beams]
---

## Definition
`max_new_tokens` bounds how many tokens the model generates in *this* call, counting only the completion
— not the prompt. Generation stops at this many new tokens or earlier if an end-of-sequence token is
produced.

## What to look for
Prefer `max_new_tokens` over the older `max_length`, because `max_length` counts prompt + completion and
so silently shrinks the allowed output as the prompt grows. Size it to the expected answer, with headroom
— roughly 2–3× — rather than to the model's full context window. If completions get cut off mid-sentence,
raise it; if latency/cost is high and answers are short, lower it. It is also the safety valve against a
model that fails to emit an EOS token and would otherwise generate until the context is exhausted.

## Calculation
The total sequence length consumed is:
$$\text{total\_tokens} = \text{prompt\_tokens} + \min(\text{generated\_tokens},\ \text{max\_new\_tokens}),$$
which must stay within the model's context window. Approximate wall-clock latency for the decode phase:
$$\text{latency} \approx \text{max\_new\_tokens} \times \text{time\_per\_token}.$$

## Example
A chat prompt is 800 tokens; you expect ~150-token replies. Set `max_new_tokens=512` (≈3× headroom), not
the model's 8192 window. At ~20 ms/token decode, a full 512-token reply takes ~10.2 s worst-case, but
most replies emit EOS around 150 tokens (~3 s). Had you set `max_new_tokens=8192`, a model that forgot to
stop could run ~164 s and burn 16× the tokens for no benefit.
