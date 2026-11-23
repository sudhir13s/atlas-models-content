---
id: model/nvidia-nemotron-3-nano-30b-a3b-bf16
type: model
slug: nvidia-nemotron-3-nano-30b-a3b-bf16
schema_version: 3.0
title: NVIDIA-Nemotron-3-Nano-30B-A3B-BF16
organization: organization/nvidia
release_date: '2025-12-04'
license_note: nvidia-nemotron-open-model-license
architecture_id: architecture-era/mixture-of-experts
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
total_parameters: 31577937344
context_window_tokens: 262144
summary: 'NVIDIA-Nemotron-3-Nano-30B-A3B-BF16 is a language model trained from scratch by NVIDIA. The card describes it as a unified model for reasoning and non-reasoning tasks: it first writes a reasoning trace and then a final answer, and the reasoning can be switched off. It is a hybrid Mixture-of-Experts model with 30B parameters in total and 3.5B active, and the card lists English, German, Spanish, French, Italian and Japanese as supported languages.'
intended_use: The card presents it as ready for commercial use, for both reasoning and non-reasoning tasks. It says that turning reasoning off may slightly lower accuracy on harder prompts that need it, and that letting the model reason first generally gives better final solutions.
what_sets_it_apart: The card describes a hybrid design of 23 Mamba-2 and mixture-of-experts layers plus 6 attention layers. Mamba-2 layers are a kind of state-space layer that handles a sequence differently from attention. Each expert layer has 128 experts plus 1 shared expert, with 6 experts active per token. The card states a maximum input and output size of 1M tokens, though the default context in the Hugging Face configuration is 256k because of the memory needed.
running_it: The card says reasoning is on by default and is turned off by passing enable_thinking=False to the chat template. It recommends temperature 1.0 and top_p 1.0 for reasoning tasks, and temperature 0.6 with top_p 0.95 for tool calling.
fingerprint:
  attention_type: GQA
  num_layers: 52
  attention_heads: 32
  kv_heads: 2
  head_size: 128
  hidden_size: 2688
  intermediate_size: 1856
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
    pattern: 23 Mamba-2 · 23 MoE · 6 attention
    note: 'Each of the 52 layers holds one mixer between its norm and its residual add: 23 layers hold a Mamba-2 state-space mixer, 23 a mixture-of-experts feed-forward and 6 attention. The configuration fixes the order, which starts Mamba-2, MoE, Mamba-2, MoE, Mamba-2, attention.'
    feed_forward: false
  positional_encoding: RoPE
  rope_theta: 10000.0
moe_config:
  expert_count: 128
  active_experts: 6
  shared_expert: true
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1213579
  likes: 840
  last_modified: '2026-08-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16
variants:
- name: nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-FP8
  format: fp8
  address: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-FP8
  publisher: nvidia
aliases:
- nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16
- nvidia-nemotron-3-nano-30b-a3b-fp8
lineage:
  parents: []
  children: []
quotes:
  summary: a large language model (LLM) trained from scratch by NVIDIA
  intended_use: This model is ready for commercial use.
  what_sets_it_apart: The model employs a hybrid Mixture-of-Experts (MoE) architecture, consisting of 23 Mamba-2 and MoE layers, along with 6 Attention layers.
  running_it: '`temperature=1.0` and `top_p=1.0` are recommended for reasoning tasks'
tags:
- nemotron-h
updated: '2026-11-23'
sources:
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16
  title: nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16/blob/main/config.json
  title: nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2512.20848
  title: arXiv:2512.20848
  trust: 0.95
- url: https://arxiv.org/abs/2512.20856
  title: arXiv:2512.20856
  trust: 0.95
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16/blob/main/modeling_nemotron_h.py
  title: nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16 — modeling_nemotron_h.py
  trust: 0.9
- url: https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16/blob/main/configuration_nemotron_h.py
  title: nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-BF16 — configuration_nemotron_h.py
  trust: 0.9
---


