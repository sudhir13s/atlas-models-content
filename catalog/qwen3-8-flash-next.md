---
id: model/qwen3-8-flash-next
type: model
slug: qwen3-8-flash-next
schema_version: 1.1
title: Qwen3.8-Flash-Next
organization: organization/qwen
release_date: '2026-08-24'
release_year: 2026
release_month: 8
license_note: qwen-community-1.0
model_category: language
tasks:
- task/chat-models
- task/vision-language-models
total_parameters: 179999981459
context_window_tokens: 262144
summary: Qwen3.8-Flash-Next is an experimental preview model from Qwen that reads text, images and video and answers in text. The card says it is a preview of the architecture that will underpin Qwen4, and gives 125B parameters with 6B activated per token, since it is a mixture-of-experts design. Its context is 262,144 tokens natively and extensible up to 1,000,000. The card names Qwen3.8-Flash as the official production version based on it.
what_sets_it_apart: Qwen lists four changes. Hybrid attention pairs Gated DeltaNet with Qwen Sparse Attention (QSA), which works on micro-blocks of tokens, and the card says this cuts long-context latency. Gated Residual controls information flowing through widened residual streams. N-gram Embedding scales parameters through embeddings indexed by short n-grams. The training recipe applies the Muon and AdamW optimizers to different weights and skips the batch-size warmup.
running_it: The card says the model thinks by default before replying, and that a direct answer comes from setting enable_thinking to false. It suggests temperature 1.0, top_p 0.95 and top_k 20 for thinking mode, and temperature 0.7, top_p 0.8, top_k 20 and a presence penalty of 1.5 for non-thinking mode. It recommends output limits of 262,144 tokens for reasoning and 131,072 for the final reply. Beyond 262,144 tokens it describes enabling YaRN, a technique for extending context length, and warns that the static form may affect shorter texts. It names SGLang, vLLM and TokenSpeed for serving.
fingerprint:
  attention_type: GQA
  num_layers: 48
  attention_heads: 24
  kv_heads: 2
  head_size: 256
  hidden_size: 2560
  context_window_tokens: 262144
  vocabulary_size: 248320
  activation_function: silu
  normalization: RMSNorm
  positional_encoding: RoPE
  rope_theta: 10000000.0
moe_config:
  expert_count: 512
  active_experts: 10
  shared_expert: true
pipeline:
  source: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py
  summary: A vision encoder turns an image or video into vectors, a patch merger joins neighbouring patches and maps them to the language model's width, and the language model reads them in the same sequence as the text tokens and writes text. Its 48 layers do not all hold the same mixer.
  side_into: merge
  stages:
  - id: text
    role: tokenizer
    title: Text tokens
    side: true
  - id: text-embedding
    role: embedding
    title: Token embedding
    details:
    - width 2560
    side: true
  - id: image
    role: input
    title: Image or video
  - id: patches
    role: embedding
    title: Patch embedding
    details:
    - 16 × 16 pixel patches · width 1152
  - id: vision-block
    role: visionEncoder
    title: Attention + feed-forward
    details:
    - 16 heads
    frame: Vision block
    repeat: 27
  - id: merger
    role: projector
    title: Patch merger
    details:
    - 4 patches into one · width 2560
  - id: merge
    role: embedding
    title: Place among text embeddings
    details:
    - at the image token positions
    note: The image vectors take the places the prompt reserves with image tokens, so text and image form one sequence.
  - id: mixer
    role: attention
    title: Gated DeltaNet or attention
    details:
    - 3 Gated DeltaNet layers, then 1
    note: 'The layers come in groups of four: three use a Gated DeltaNet, a linear-attention mixer, and the fourth uses attention with 24 query heads and 2 key-value heads. Each result is added back through a learned gated residual connection, and some layers first add a per-layer embedding of the input tokens.'
    frame: Layer
    repeat: 48
  - id: experts
    role: experts
    title: Mixture of experts
    details:
    - 512 experts · 10 active per token
    frame: Layer
  - id: output
    role: outputHead
    title: Output head
    details:
    - logits over 248,320
filters:
  size_category: xlarge
  is_quantized: false
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: image-text-to-text
  library: transformers
hub_signals:
  downloads_30d: 2727999
  likes: 7522
  last_modified: '2026-11-07'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Qwen/Qwen3.8-Flash-Next
aliases:
- Qwen/Qwen3.8-Flash-Next
lineage:
  parents: []
  children: []
quotes:
  summary: This experimental preview of the architecture that will underpin Qwen4 is built around a fundamental rethinking of how the core components of modern large language models (LLMs) interact at scale.
  what_sets_it_apart: Rather than selecting individual tokens for processing, QSA operates at the micro-block level.
  running_it: Qwen3.8-Flash-Next will think by default before responding.
tags:
- llm
- image-text-to-text
- qwen4-exp
- moe
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3.8-Flash-Next
  title: Qwen/Qwen3.8-Flash-Next — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3.8-Flash-Next/blob/main/config.json
  title: Qwen/Qwen3.8-Flash-Next — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen4_exp/modeling_qwen4_exp.py
  title: 'transformers/models/qwen4_exp/modeling_qwen4_exp.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Qwen3.8-Flash-Next is an experimental preview model from Qwen that reads text, images and video and answers in text. The card says it is a preview of the architecture that will underpin Qwen4, and gives 125B parameters with 6B activated per token, since it is a mixture-of-experts design. Its context is 262,144 tokens natively and extensible up to 1,000,000. The card names Qwen3.8-Flash as the official production version based on it.

## What Sets It Apart

Qwen lists four changes. Hybrid attention pairs Gated DeltaNet with Qwen Sparse Attention (QSA), which works on micro-blocks of tokens, and the card says this cuts long-context latency. Gated Residual controls information flowing through widened residual streams. N-gram Embedding scales parameters through embeddings indexed by short n-grams. The training recipe applies the Muon and AdamW optimizers to different weights and skips the batch-size warmup.

## Running It

The card says the model thinks by default before replying, and that a direct answer comes from setting enable_thinking to false. It suggests temperature 1.0, top_p 0.95 and top_k 20 for thinking mode, and temperature 0.7, top_p 0.8, top_k 20 and a presence penalty of 1.5 for non-thinking mode. It recommends output limits of 262,144 tokens for reasoning and 131,072 for the final reply. Beyond 262,144 tokens it describes enabling YaRN, a technique for extending context length, and warns that the static form may affect shorter texts. It names SGLang, vLLM and TokenSpeed for serving.
