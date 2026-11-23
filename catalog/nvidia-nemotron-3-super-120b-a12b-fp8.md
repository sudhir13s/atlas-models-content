---
id: model/nvidia-nemotron-3-super-120b-a12b-fp8
type: model
slug: nvidia-nemotron-3-super-120b-a12b-fp8
schema_version: 3.0
title: NVIDIA-Nemotron-3-Super-120B-A12B-FP8
organization: organization/nvidia
release_date: '2026-03-10'
license_note: nvidia-nemotron-open-model-license
architecture_id: architecture-era/mixture-of-experts
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
total_parameters: 123611012096
context_window_tokens: 1048576
summary: NVIDIA-Nemotron-3-Super-120B-A12B-FP8 is a large language model trained by NVIDIA, with 120 billion parameters in total of which 12 billion are active for each token. The card says it answers by first writing a reasoning trace and then a final response, and that reasoning can be switched on or off through a flag in the chat template. It lists support for English, French, German, Italian, Japanese, Spanish and Chinese and a context length of up to 1 million tokens.
intended_use: NVIDIA describes a general-purpose reasoning and chat model for English, code and supported multilingual use, optimized for collaborative agents and high-volume workloads. It is meant for developers building AI agent systems, chatbots and retrieval-augmented generation (RAG) systems, and is also described as suitable for complex instruction following and long-context reasoning.
what_sets_it_apart: The card describes a hybrid design that interleaves Mamba-2 layers (a sequence-modelling layer that is not attention) with mixture-of-experts layers and a few attention layers. In its LatentMoE design, tokens are projected into a smaller latent dimension for expert routing. It adds Multi-Token Prediction (MTP) layers, which enable faster generation through speculative decoding, and it states the model was pre-trained with NVFP4, a 4-bit number format, which it says is a first in the Nemotron 3 family.
running_it: The card says to use temperature=1.0 and top_p=0.95 for all tasks and serving backends, including reasoning, tool calling and chat. It lists a minimum GPU requirement of 2 H100-80GB cards, and gives quick-start instructions for vLLM, SGLang and TRT-LLM.
fingerprint:
  attention_type: GQA
  num_layers: 88
  attention_heads: 32
  kv_heads: 2
  head_size: 128
  hidden_size: 4096
  intermediate_size: 2688
  vocabulary_size: 131072
  activation_function: relu2
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  layer_mixers:
    names:
    - Mamba-2
    - MoE
    - attention
    pattern: 40 Mamba-2 · 40 MoE · 8 attention
    note: 'Each of the 88 layers holds one mixer between its norm and its residual add: 40 layers hold a Mamba-2 state-space mixer, 40 a mixture-of-experts feed-forward and 8 attention. The configuration fixes the order, which starts Mamba-2, MoE, Mamba-2, MoE, Mamba-2, MoE, Mamba-2, attention.'
    feed_forward: false
  positional_encoding: RoPE
  rope_theta: 10000.0
moe_config:
  expert_count: 512
  active_experts: 22
  shared_expert: true
filters:
  size_category: xlarge
  is_quantized: true
  quant_format: modelopt
  is_moe: true
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 27344
  likes: 293
  last_modified: '2026-08-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8
aliases:
- nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8
lineage:
  parents: []
  children: []
quotes:
  summary: The model's reasoning capabilities can be configured through a flag in the chat template.
  intended_use: It is intended to be used by developers designing AI Agent systems, chatbots, RAG systems, and other AI-powered applications.
  what_sets_it_apart: tokens are projected into a smaller latent dimension for expert routing and computation
  running_it: Use `temperature=1.0` and `top_p=0.95` across
locked:
- context_window_tokens
tags:
- nemotron-h
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8
  title: nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8/blob/main/config.json
  title: nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2512.20848
  title: arXiv:2512.20848
  trust: 0.95
- url: https://arxiv.org/abs/2512.20856
  title: arXiv:2512.20856
  trust: 0.95
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8/blob/main/modeling_nemotron_h.py
  title: nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8 — modeling_nemotron_h.py
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8/blob/main/configuration_nemotron_h.py
  title: nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-FP8 — configuration_nemotron_h.py
  trust: 0.9
---


