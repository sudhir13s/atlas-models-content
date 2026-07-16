---
id: model/<slug>
type: model
slug: <slug>
schema_version: 1.0
title: "<Human Readable Model Name>"
organization: "<org>"
release_date: "<YYYY-MM-DD>"
release_year: <YYYY>
license: license/<license-slug>
signal_strength: medium          # high | medium | low
architecture_id: architecture-era/<slug>
model_category: language
parameters_active_b: <number>
total_parameters: <int>
context_window_tokens: <int>
summary: "<one-sentence architecture summary>"
why_it_matters: "<one sentence on why this model matters>"
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
  pipeline_tag: text-generation
  attention_type: <...>
benchmarks:
  - { name: MMLU, score: 0.0, score_display: "0.0%", method: "5-shot", self_reported: true, source_url: "<url>" }
lineage:
  parents: [model/<base-slug>]
  children: []
tags: [<tag>, <tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
related: [architecture-era/<slug>]
---

## Summary
<2-3 sentence description of the architecture.>

## Why It Matters
<1-2 paragraphs on the model's significance.>

## Notes
<Notable configuration details, gotchas, deployment notes.>
