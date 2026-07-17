---
id: hyperparameter/do-sample
type: hyperparameter
slug: do-sample
schema_version: 1.0
title: "Do Sample"
name: do_sample
context: inference
priority: 6
importance: important
goals: [inference]
models: [llm]
range: "true | false"
starting_value: "true (for temperature/top-p/top-k to have any effect); false for deterministic greedy"
controls: "The master switch between stochastic sampling and deterministic greedy decoding."
if_increased: "true enables sampling — temperature, top_p, and top_k become active and output varies run to run."
if_decreased: "false forces greedy (argmax) decoding — deterministic, and all sampling knobs are ignored."
common_mistake: "Setting temperature and top_p but leaving do_sample=false — the model greedily takes the argmax and silently ignores every sampling parameter, a very common 'why won't my temperature work' bug."
tags: [inference, sampling, decoding, greedy]
updated: 2026-07-17
sources:
  - { url: "https://huggingface.co/docs/transformers/generation_strategies", title: "Hugging Face — text generation strategies", trust: 0.85 }
  - { url: "https://huggingface.co/docs/transformers/main_classes/text_generation", title: "Hugging Face — GenerationConfig (do_sample)", trust: 0.85 }
related: [hyperparameter/temperature, hyperparameter/top-p, hyperparameter/top-k]
---

## Definition
`do_sample` selects the decoding mode. When `false`, generation is **greedy**: each step deterministically
picks the highest-probability token. When `true`, generation **samples** from the (temperature/top-p/
top-k-shaped) distribution, so repeated calls can differ.

## What to look for
This flag gates all the randomness knobs. If `do_sample=false`, then `temperature`, `top_p`, and `top_k`
have **no effect** — the decoder takes the argmax regardless. So the first thing to check when
temperature "isn't working" is whether sampling is even on. Use `do_sample=false` when you need
reproducible, deterministic output (extraction, classification, tests); use `do_sample=true` for chat,
brainstorming, and anything where variety helps. Note that beam search (`num_beams>1`) is yet another
mode, separate from both.

## Calculation
Greedy (`do_sample=false`): $\text{next} = \arg\max_i p_i$.
Sampling (`do_sample=true`): draw $\text{next} \sim p'$, where $p'$ is the distribution after temperature
scaling and top-p/top-k truncation. Greedy is the $T\to 0$ limit of sampling, but is reached via the flag,
not by setting temperature to 0 in every framework.

## Example
Config `{temperature: 0.9, top_p: 0.95, do_sample: false}` looks creative but is not: because
`do_sample=false`, the decoder ignores both and returns the identical greedy completion every time. Flip
to `do_sample=true` and the same `temperature=0.9`/`top_p=0.95` now shape a distribution the model samples
from, producing varied, more natural output. Conversely, for a deterministic JSON-extraction endpoint,
set `do_sample=false` and the temperature value becomes irrelevant.
