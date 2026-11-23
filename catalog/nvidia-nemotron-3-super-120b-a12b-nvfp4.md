---
id: model/nvidia-nemotron-3-super-120b-a12b-nvfp4
type: model
slug: nvidia-nemotron-3-super-120b-a12b-nvfp4
schema_version: 1.1
title: NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4
organization: organization/nvidia
release_date: '2026-03-10'
release_year: 2026
release_month: 3
license_note: nvidia-nemotron-open-model-license
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 67228556288
context_window_tokens: 1048576
summary: NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4 is the NVFP4 version of Nemotron-3-Super, a large language model from NVIDIA with 120B total and 12B active parameters, meaning only part of the weights is used for each token. NVFP4 is a reduced-precision number format, and the card says the Super model was pre-trained with it. The card lists a context of up to 1M tokens, seven supported languages (English, French, German, Italian, Japanese, Spanish and Chinese), and a reasoning mode that can be switched on or off.
intended_use: The card calls it a general purpose reasoning and chat model for English, code and the supported languages, optimized for collaborative agents and high-volume workloads such as IT ticket automation. It names developers building AI agent systems, chatbots, RAG (retrieval-augmented generation) systems and other applications as the intended users, and lists complex instruction following and long-context reasoning as suitable tasks.
what_sets_it_apart: NVIDIA describes a hybrid design that interleaves Mamba-2 layers and mixture-of-experts layers with a few attention layers. Its LatentMoE method projects tokens into a smaller latent dimension for expert routing and computation. It adds Multi-Token Prediction (MTP) layers, which allow faster generation through speculative decoding. The card says it is the first model in the Nemotron 3 family pre-trained at NVFP4 precision.
running_it: The card says to use temperature 1.0 and top_p 0.95 for all tasks and serving backends. Reasoning is switched on or off with the enable_thinking flag in the chat template, and each serving backend needs NVIDIA's custom super_v3 reasoning parser. The card lists one B200 GPU or one DGX Spark as the minimum hardware and NVIDIA Blackwell as the supported microarchitecture.
fingerprint:
  attention_type: GQA
  num_layers: 88
  attention_heads: 32
  kv_heads: 2
  head_size: 128
  hidden_size: 4096
  intermediate_size: 2688
  context_window_tokens: 1048576
  vocabulary_size: 131072
  activation_function: relu2
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  layer_mixers: {"names": ["Mamba-2", "MoE", "attention"], "pattern": "40 Mamba-2 · 40 MoE · 8 attention", "note": "Each of the 88 layers holds one mixer between its norm and its residual add: 40 layers hold a Mamba-2 state-space mixer, 40 a mixture-of-experts feed-forward and 8 attention. The configuration fixes the order, which starts Mamba-2, MoE, Mamba-2, MoE, Mamba-2, MoE, Mamba-2, attention.", "feed_forward": false}
  positional_encoding: RoPE
  rope_theta: 10000.0
moe_config:
  expert_count: 512
  active_experts: 22
  shared_expert: true
filters:
  size_category: large
  is_quantized: true
  quant_format: modelopt
  is_moe: true
  attention_type: GQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 473577
  likes: 454
  last_modified: '2026-08-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4
aliases:
- nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4
lineage:
  parents: []
  children: []
quotes:
  summary: is a large language model (LLM) trained by NVIDIA
  intended_use: It is intended to be used by developers designing AI Agent systems, chatbots, RAG systems, and other AI-powered applications.
  what_sets_it_apart: The Super model is pre-trained using NVFP4 quantization — the first model in the Nemotron 3 family trained at this precision.
  running_it: Use `temperature=1.0` and `top_p=0.95` across **all tasks and serving backends**
locked:
- context_window_tokens
- fingerprint.context_window_tokens
tags:
- llm
- text-generation
- nemotron-h
- moe
- gqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4
  title: nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4/blob/main/config.json
  title: nvidia/NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4 — config.json
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

## Summary

NVIDIA-Nemotron-3-Super-120B-A12B-NVFP4 is the NVFP4 version of Nemotron-3-Super, a large language model from NVIDIA with 120B total and 12B active parameters, meaning only part of the weights is used for each token. NVFP4 is a reduced-precision number format, and the card says the Super model was pre-trained with it. The card lists a context of up to 1M tokens, seven supported languages (English, French, German, Italian, Japanese, Spanish and Chinese), and a reasoning mode that can be switched on or off.

## Intended Use

The card calls it a general purpose reasoning and chat model for English, code and the supported languages, optimized for collaborative agents and high-volume workloads such as IT ticket automation. It names developers building AI agent systems, chatbots, RAG (retrieval-augmented generation) systems and other applications as the intended users, and lists complex instruction following and long-context reasoning as suitable tasks.

## What Sets It Apart

NVIDIA describes a hybrid design that interleaves Mamba-2 layers and mixture-of-experts layers with a few attention layers. Its LatentMoE method projects tokens into a smaller latent dimension for expert routing and computation. It adds Multi-Token Prediction (MTP) layers, which allow faster generation through speculative decoding. The card says it is the first model in the Nemotron 3 family pre-trained at NVFP4 precision.

## Running It

The card says to use temperature 1.0 and top_p 0.95 for all tasks and serving backends. Reasoning is switched on or off with the enable_thinking flag in the chat template, and each serving backend needs NVIDIA's custom super_v3 reasoning parser. The card lists one B200 GPU or one DGX Spark as the minimum hardware and NVIDIA Blackwell as the supported microarchitecture.
