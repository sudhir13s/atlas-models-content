---
id: model/deepseek-v2-lite-chat
type: model
slug: deepseek-v2-lite-chat
schema_version: 1.1
title: DeepSeek-V2-Lite-Chat
organization: organization/deepseek
release_date: '2024-05-15'
release_year: 2024
release_month: 5
license: license/deepseek
architecture_id: architecture-era/mixture-of-experts
model_category: language
tasks:
- task/text-generation
total_parameters: 15706484224
context_window_tokens: 32768
summary: 'DeepSeek-V2-Lite-Chat is a conversational language model from DeepSeek. It is the chat version of DeepSeek-V2-Lite, a Mixture-of-Experts model, which means only some of its parameters are used for each token: the card lists 16B in total and 2.4B active. The chat version was made by supervised fine-tuning, training on example conversations, after pre-training and a long-context extension, and the card lists a context length of 32k.'
intended_use: The card says that after the release of DeepSeek-V2, community members suggested open-sourcing a smaller Mixture-of-Experts model for in-depth research, and DeepSeek-V2-Lite is that release. It states that the model can be deployed on a single 40G GPU and fine-tuned on 8 GPUs of 80G, and that the V2 series supports commercial use under the Model License.
what_sets_it_apart: The card highlights two design choices. Multi-head Latent Attention compresses the key-value cache, the stored attention data for earlier tokens, into a smaller latent vector so inference is efficient. DeepSeekMoE uses sparse computation, so each token passes through a few experts only. For the Lite model the card gives 27 layers, 2 shared experts and 64 routed experts per mixture layer with 6 routed experts active per token, and pre-training from scratch on 5.7T tokens.
fingerprint:
  attention_type: MLA
  num_layers: 27
  attention_heads: 16
  kv_heads: 16
  head_size: 128
  hidden_size: 2048
  intermediate_size: 10944
  context_window_tokens: 32768
  vocabulary_size: 102400
  activation_function: silu
  normalization: RMSNorm
  norm_placement: pre
  stack: decoder
  positional_encoding: RoPE
  rope_theta: 10000.0
moe_config:
  expert_count: 64
  active_experts: 6
  shared_expert: true
filters:
  size_category: large
  is_quantized: false
  is_moe: true
  attention_type: MLA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 67508
  likes: 173
  last_modified: '2024-06-25'
  observed_at: '2026-11-20'
  source: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat
aliases:
- deepseek-ai/DeepSeek-V2-Lite-Chat
lineage:
  parents: []
  children: []
quotes:
  summary: 16B total params, 2.4B active params, scratch training with 5.7T tokens
  intended_use: Many in the community suggested open-sourcing a smaller MoE model for in-depth research.
  what_sets_it_apart: MLA guarantees efficient inference through significantly compressing the Key-Value (KV) cache into a latent vector
locked:
- context_window_tokens
- fingerprint.context_window_tokens
tags:
- llm
- text-generation
- deepseek-v2
- moe
- mla
updated: '2026-11-23'
sources:
- url: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat
  title: deepseek-ai/DeepSeek-V2-Lite-Chat — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/deepseek-ai/DeepSeek-V2-Lite-Chat/blob/main/config.json
  title: deepseek-ai/DeepSeek-V2-Lite-Chat — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2405.04434
  title: arXiv:2405.04434
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v2/modeling_deepseek_v2.py
  title: 'transformers/models/deepseek_v2/modeling_deepseek_v2.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

DeepSeek-V2-Lite-Chat is a conversational language model from DeepSeek. It is the chat version of DeepSeek-V2-Lite, a Mixture-of-Experts model, which means only some of its parameters are used for each token: the card lists 16B in total and 2.4B active. The chat version was made by supervised fine-tuning, training on example conversations, after pre-training and a long-context extension, and the card lists a context length of 32k.

## Intended Use

The card says that after the release of DeepSeek-V2, community members suggested open-sourcing a smaller Mixture-of-Experts model for in-depth research, and DeepSeek-V2-Lite is that release. It states that the model can be deployed on a single 40G GPU and fine-tuned on 8 GPUs of 80G, and that the V2 series supports commercial use under the Model License.

## What Sets It Apart

The card highlights two design choices. Multi-head Latent Attention compresses the key-value cache, the stored attention data for earlier tokens, into a smaller latent vector so inference is efficient. DeepSeekMoE uses sparse computation, so each token passes through a few experts only. For the Lite model the card gives 27 layers, 2 shared experts and 64 routed experts per mixture layer with 6 routed experts active per token, and pre-training from scratch on 5.7T tokens.
