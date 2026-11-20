---
id: model/<slug>
type: model
slug: <slug>
schema_version: 1.1
title: "<Human Readable Model Name>"
organization: organization/<organization-slug>
release_date: "<YYYY-MM-DD>"
release_year: <YYYY>
release_month: <M>
license: license/<license-slug>   # or `license_note: "<licence name as the source gives it>"` when no license record exists
signal_strength: medium          # high | medium | low
architecture_id: architecture-era/<slug>
model_category: language
parameters_active_b: <number>
total_parameters: <int>
context_window_tokens: <int>
summary: "<2-3 sentences: what the model is, its family or base model, what it was built to do>"   # omit the five passages, quotes and the body for a record with no prose yet
intended_use: "<1-3 sentences: the tasks and users the source names, and uses it rules out>"   # a passage the source cannot support is omitted, never written as "unknown"
what_sets_it_apart: "<1-3 sentences: the design choice, training approach or reported result that separates it from its siblings or predecessors>"
running_it: "<1-2 sentences: recommended settings, prompt format, hardware, what must or must not be done>"
limitations: "<1-2 sentences: what the source says the model does badly, does not cover, or risks>"
why_it_matters: "<one sentence on why this model matters>"   # hand-written records only; the prose queue does not write it
quotes:                          # set by the prose queue: one span of the source per written passage
  summary: "<span of the source text>"
  intended_use: "<span of the source text>"
  what_sets_it_apart: "<span of the source text>"
  running_it: "<span of the source text>"
  limitations: "<span of the source text>"
fingerprint:
  attention_type: <MHA | GQA | MQA | MLA>
  num_layers: <int>
  attention_heads: <int>
  kv_heads: <int>
  head_size: <int>
  hidden_size: <int>
  intermediate_size: <int>
  context_window_tokens: <int>
  vocabulary_size: <int>
  activation_function: <SwiGLU | GELU | ...>
  normalization: <RMSNorm | LayerNorm>
  positional_encoding: <RoPE | ALiBi | learned>
  rope_theta: <number | null>
  parameters_active_b: <number>
  kv_cache_rating: <low | medium | high>
  key_detail: "<notable detail>"
moe_config: null                 # or { expert_count, active_experts, shared_expert, notes }
filters:
  size_category: <tiny | small | medium | large | xlarge>
  is_quantized: false
  quant_format: original
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
  attention_type: <...>
hub_signals:                     # owned by the jobs; numbers that change
  downloads_30d: <int>
  likes: <int>
  last_modified: <YYYY-MM-DD>
  observed_at: <YYYY-MM-DD>
  source: "<url>"
variants:                        # quantised or repackaged copies, one entry each
  - { name: "<owner/repo>", format: awq, address: "https://huggingface.co/<owner/repo>", publisher: <hf-handle> }
aliases: ["<owner/repo>"]
lineage:
  parents: [model/<base-slug>]
  children: []
locked: [summary, why_it_matters]  # field paths no job may change (any passage field, or `body`)
tags: [<tag>, <tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
related: [architecture-era/<slug>]
---

## Summary
<2-3 sentences: what the model is.>

## Intended Use
<1-3 sentences: tasks and users, and uses ruled out.>

## What Sets It Apart
<1-3 sentences: what separates it from its siblings, base model or predecessors.>

## Running It
<1-2 sentences: settings, prompt format, hardware.>

## Limitations
<1-2 sentences: what it does badly, does not cover, or risks.>

## Why It Matters
<1-2 paragraphs on the model's significance; hand-written records only.>

## Notes
<Notable configuration details, gotchas, deployment notes.>
