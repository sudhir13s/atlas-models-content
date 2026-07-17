---
id: model/qwen2-5-72b-instruct
type: model
slug: qwen2-5-72b-instruct
schema_version: 1.0
title: "Qwen2.5 72B Instruct"
organization: "Alibaba (Qwen Team)"
release_date: "2024-09-19"
release_year: 2024
release_month: 9
license: license/qwen
signal_strength: high
architecture_id: architecture-era/transformer
model_category: language
parameters_active_b: 72.71
total_parameters: 72706203648
context_window_tokens: 131072
summary: "Dense decoder-only transformer, ~72.7B parameters, 128K context via YaRN, using GQA, SwiGLU, RMSNorm and RoPE."
why_it_matters: "The open-weight frontier model of 2024 — it rivalled Llama 3.1 405B on many tasks at a sixth of the parameters."
fingerprint:
  attention_type: GQA
  num_layers: 80
  attention_heads: 64
  kv_heads: 8
  head_size: 128
  hidden_size: 8192
  intermediate_size: 29568
  context_window_tokens: 131072
  vocabulary_size: 152064
  activation_function: SwiGLU
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 1000000
  parameters_active_b: 72.71
  kv_cache_rating: high
  key_detail: "80 layers, 64 query / 8 KV heads; QKV projections carry bias; 128K context via YaRN on a 32768-trained base"
moe_config: null
filters:
  size_category: large
  is_quantized: false
  quant_format: original
  is_moe: false
  pipeline_tag: text-generation
  attention_type: GQA
benchmarks:
  - { name: MMLU-Pro, score: 0.711, score_display: "71.1%", method: "5-shot", self_reported: true, source_url: "https://qwenlm.github.io/blog/qwen2.5-llm/" }
  - { name: GSM8K, score: 0.958, score_display: "95.8%", method: "vendor-reported", self_reported: true, source_url: "https://qwenlm.github.io/blog/qwen2.5-llm/" }
  - { name: HumanEval, score: 0.866, score_display: "86.6%", method: "0-shot", self_reported: true, source_url: "https://qwenlm.github.io/blog/qwen2.5-llm/" }
  - { name: MATH, score: 0.831, score_display: "83.1%", method: "vendor-reported", self_reported: true, source_url: "https://qwenlm.github.io/blog/qwen2.5-llm/" }
lineage:
  parents: []
  children: []
tags: [llm, qwen, alibaba, dense, gqa, instruct, multilingual, frontier]
updated: 2026-07-17
sources:
  - { url: "https://huggingface.co/Qwen/Qwen2.5-72B-Instruct", title: "Qwen2.5-72B-Instruct — model card + config.json", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2412.15115", title: "Qwen2.5 Technical Report", trust: 0.95 }
related: [architecture-era/transformer, license/qwen]
---

## Summary
Qwen2.5 72B Instruct is a dense, decoder-only transformer with 80 layers and an 8192-dimensional
residual stream. It uses grouped-query attention (64 query heads sharing 8 key/value heads, head size
128), SwiGLU feed-forward blocks with an intermediate size of 29568, RMSNorm pre-normalization, and
rotary positional embeddings with a base frequency of 1000000. As in the rest of the Qwen2 family the
query/key/value projections carry additive bias terms. The 128K-token context is obtained through
YaRN RoPE scaling at inference over a 32768-token-trained base.

## Why It Matters
This was the strongest fully open-weight model of 2024: across MMLU-Pro, MATH, code, and instruction
benchmarks it matched or beat Llama 3.1 405B while using roughly one-sixth the parameters, and it
comfortably outperformed GPT-4o-mini-class systems. That made frontier-adjacent quality runnable on a
single 8×A100/H100 node (or two consumer GPUs when quantized to 4-bit), which is why it became the
default self-hosted "large" model for teams that wanted control over their stack.

## Notes
Unlike the smaller Apache-2.0 Qwen2.5 checkpoints, the 72B release ships under the custom Qwen license
(commercial use permitted, with additional terms above certain usage thresholds — see
[[license/qwen]]). The base `config.json` reports `max_position_embeddings: 32768`; the 128K window
requires enabling YaRN scaling at load time. Chat formatting uses the ChatML template. See
[[architecture-era/transformer]] for the architecture lineage.
