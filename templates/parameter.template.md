---
id: parameter/<slug>
type: parameter
slug: <slug>
schema_version: 3.1              # the schema's current version; the schema refuses any other
title: "<Parameter Name>"
name: <config_key>               # the exact key in config.json
priority: <int>                  # 1 = most impactful
importance: critical             # critical | important | optional
applies_to: [llm, transformer]   # architecture families
range: "<low — high>"            # spans typical_values
controls: "<one plain sentence: what the parameter is>"
effects:
  decrease: ["<what changes when it goes down>"]
  increase: ["<what changes when it goes up>"]
scale: { min: <number>, max: <number>, typical_min: <number>, typical_max: <number>, spacing: log }
typical_values:                  # one row per model, each from that model's config.json, card or paper
  - setting: "<Model> (config.json)"
    value: "<one plain number>"
    source: "<url of one entry of sources>"
mistakes:                        # two to five, drawn as common misunderstandings
  - mistake: "<the misunderstanding>"
    why: "<what the source shows instead>"
    fix: "<what to do>"
related: [parameter/<slug>]
visual:                          # count_sweep | count_breakdown | head_grid | kv_cache | context_cost
  kind: count_sweep
  model: { name: "<Model>", vocabulary_size: <int>, hidden_size: <int>, intermediate_size: <int>, num_layers: <int>, attention_heads: <int>, kv_heads: <int>, head_size: <int>, tie_word_embeddings: false, ffn_matrices: 3, qk_norm: false }
  vary: width
  values: [<number>, <number>]
  measure: per_layer
  caption: "<what is drawn and whose config.json it uses>"
tags: [<tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.95 }
---

## Definition

<Under the figure: the parameter in plain words, each term explained at first use.>

## How It Works

<Numbered steps: where the value is used as data passes through the model.>

## Calculation

<How the key enters the parameter count, said in words first, every symbol in a table; link the
parameter calculators.>

## Worked Example

<A catalog model's own numbers, named, every step numbered with its result; close with "What this
means".>

## What To Watch

<Labelled bullets: what to check in a configuration.>
