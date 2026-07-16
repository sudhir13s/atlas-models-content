---
id: hyperparameter/<slug>
type: hyperparameter
slug: <slug>
schema_version: 1.0
title: "<Human Readable Name>"
name: <exact_config_key>
context: training                # training | inference
priority: <int>                  # criticality rank within context; 1 = most impactful
importance: critical             # critical | important | optional
goals: [training, finetuning]
models: [llm]
range: "<typical range>"
starting_value: "<sensible default>"
controls: "<what it governs, one line>"
if_increased: "<directional effect>"
if_decreased: "<directional effect>"
common_mistake: "<the classic error>"
tags: [<tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
related: [hyperparameter/<slug>]
---

## Definition
<One clear sentence: what it is.>

## What to look for
<How to read it in a config; what good vs bad values signal.>

## Calculation
<Typical range, formula (LaTeX ok), and how it is computed.>

## Example
<A concrete, worked example with real numbers.>
