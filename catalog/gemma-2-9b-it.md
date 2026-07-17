---
id: model/gemma-2-9b-it
type: model
slug: gemma-2-9b-it
schema_version: 1.0
title: "Gemma 2 9B IT"
organization: "Google DeepMind"
release_date: "2024-06-27"
release_year: 2024
release_month: 6
license: license/gemma
signal_strength: high
architecture_id: architecture-era/transformer
model_category: language
parameters_active_b: 9.24
total_parameters: 9241705984
context_window_tokens: 8192
summary: "Dense decoder-only transformer, ~9.24B parameters, 8K context, using GQA, alternating local/global attention, logit soft-capping, GeGLU, RMSNorm and RoPE."
why_it_matters: "Google's strongest small open model of 2024, and the reference example of alternating local/global attention with attention soft-capping."
fingerprint:
  attention_type: GQA
  num_layers: 42
  attention_heads: 16
  kv_heads: 8
  head_size: 256
  hidden_size: 3584
  intermediate_size: 14336
  context_window_tokens: 8192
  vocabulary_size: 256000
  activation_function: GeGLU
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000
  parameters_active_b: 9.24
  kv_cache_rating: medium
  key_detail: "Alternates local sliding-window (4096) and global attention every other layer; attn logit soft-cap 50.0, final logit soft-cap 30.0; head_dim 256 (decoupled from hidden_size); pre- and post-RMSNorm on each sublayer"
moe_config: null
filters:
  size_category: small
  is_quantized: false
  quant_format: original
  is_moe: false
  pipeline_tag: text-generation
  attention_type: GQA
benchmarks:
  - { name: MMLU, score: 0.713, score_display: "71.3%", method: "5-shot (Gemma 2 9B pretrained-base report)", self_reported: true, source_url: "https://arxiv.org/abs/2408.00118" }
  - { name: GSM8K, score: 0.686, score_display: "68.6%", method: "5-shot (Gemma 2 9B pretrained-base report)", self_reported: true, source_url: "https://arxiv.org/abs/2408.00118" }
  - { name: HumanEval, score: 0.402, score_display: "40.2%", method: "pass@1 (Gemma 2 9B pretrained-base report)", self_reported: true, source_url: "https://arxiv.org/abs/2408.00118" }
  - { name: MATH, score: 0.366, score_display: "36.6%", method: "4-shot (Gemma 2 9B pretrained-base report)", self_reported: true, source_url: "https://arxiv.org/abs/2408.00118" }
lineage:
  parents: []
  children: []
tags: [llm, gemma, google, dense, gqa, instruct, sliding-window]
updated: 2026-07-17
sources:
  - { url: "https://huggingface.co/google/gemma-2-9b-it", title: "Gemma 2 9B IT — model card", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2408.00118", title: "Gemma 2: Improving Open Language Models at a Practical Size", trust: 0.95 }
related: [architecture-era/transformer, license/gemma]
---

## Summary
Gemma 2 9B IT is a dense, decoder-only transformer with 42 layers and a 3584-dimensional residual
stream. It uses grouped-query attention (16 query heads sharing 8 key/value heads) with a head
dimension of 256 that is deliberately decoupled from the hidden size, GeGLU feed-forward blocks
(intermediate size 14336), and rotary positional embeddings. Two architectural signatures set it
apart: attention layers alternate between a local sliding window of 4096 tokens and full global
attention every other layer, and both the attention logits (soft-capped at 50.0) and the final output
logits (soft-capped at 30.0) are passed through a `tanh` soft-cap to stabilize training. Each sublayer
is wrapped in both pre- and post-RMSNorm.

## Why It Matters
Gemma 2 9B was Google's answer at the sub-10B open tier, beating Llama 3 8B on most knowledge and
reasoning benchmarks and shipping with knowledge-distillation-trained weights (the 9B was distilled
from a larger teacher rather than trained from scratch on tokens alone). Architecturally it is the
canonical reference for two ideas that later spread widely: interleaving cheap local attention with
periodic global attention to bound compute, and logit soft-capping to keep attention scores and output
distributions from saturating. The 256-dimensional heads (larger than the 128 typical of peers) give
each head more representational capacity.

## Notes
The weights are gated on Hugging Face and governed by the custom Gemma Terms of Use (commercial use
permitted, subject to a prohibited-use policy — see [[license/gemma]]). The 8K context is short by
2024 standards and is a real constraint for long-document RAG. The benchmark figures above are the
Gemma 2 report's numbers for the 9B *pretrained base*; the instruction-tuned checkpoint's scores
differ. Soft-capping must be enabled for faithful inference — some early inference stacks silently
dropped it, degrading quality. See [[architecture-era/transformer]] for the architecture lineage.
