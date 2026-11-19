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
summary: "<one-sentence architecture summary>"   # omit summary, why_it_matters and the body for a record with no prose yet
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
locked: [summary, why_it_matters]  # field paths no job may change
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
