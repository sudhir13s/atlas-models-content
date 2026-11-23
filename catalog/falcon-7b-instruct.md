---
id: model/falcon-7b-instruct
type: model
slug: falcon-7b-instruct
schema_version: 1.1
title: falcon-7b-instruct
organization: organization/technology-innovation-institute
release_date: '2023-04-25'
release_year: 2023
release_month: 4
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
tasks:
- task/text-generation
parameters_active_b: 7.22
total_parameters: 7217189760
base_models:
- name: tiiuae/falcon-7b
  address: https://huggingface.co/tiiuae/falcon-7b
summary: Falcon-7B-Instruct is a chat model built by TII. The card says it is based on TII's own Falcon-7B model, which TII fine-tuned on a mixture of chat and instruction datasets so that it answers questions and follows requests. The card calls it a causal decoder-only model with 7 billion parameters, meaning it reads text and predicts the next word, and lists English and French as its languages.
intended_use: The card says it is for direct use as a chat or instruction model, fine-tuned on a mixture of instruct and chat datasets. It names production use without adequate assessment of risks and mitigation, and any irresponsible or harmful use, as out of scope, and advises users to build guardrails.
what_sets_it_apart: The card says the architecture is optimized for inference, using FlashAttention and multiquery attention, two techniques that make attention cheaper to compute and its memory smaller. It states the Falcon-7B base was trained on 1,500B tokens of RefinedWeb, a web-crawl dataset, plus curated corpora, and that the instruct version was fine-tuned on a mixture of about 250M tokens of chat and instruction data.
running_it: The card says you need at least 16GB of memory to run inference swiftly and PyTorch 2.0 to use it with Transformers. It also says this instruct model may not be ideal for further fine-tuning, and suggests starting from Falcon-7B to build your own chat model.
fingerprint:
  attention_type: MQA
  num_layers: 32
  attention_heads: 71
  kv_heads: 1
  head_size: 64
  hidden_size: 4544
  vocabulary_size: 65024
  normalization: LayerNorm
  norm_placement: pre
  stack: decoder
  parallel_sublayers: shared-norm
  positional_encoding: RoPE
filters:
  size_category: medium
  is_quantized: false
  is_moe: false
  attention_type: MQA
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 49420
  likes: 1042
  last_modified: '2024-10-12'
  observed_at: '2026-11-20'
  source: https://huggingface.co/tiiuae/falcon-7b-instruct
aliases:
- tiiuae/falcon-7b-instruct
lineage:
  parents: []
  children: []
quotes:
  summary: Falcon-7B-Instruct is a 7B parameters causal decoder-only model built by [TII](https://www.tii.ae) based on [Falcon-7B](https://huggingface.co/tiiuae/falcon-7b) and finetuned on a mixture of chat/instruct datasets.
  intended_use: Production use without adequate assessment of risks and mitigation; any use cases which may be considered irresponsible or harmful.
  what_sets_it_apart: It features an architecture optimized for inference
  running_it: You will need **at least 16GB of memory** to swiftly run inference with Falcon-7B-Instruct.
tags:
- llm
- text-generation
- falcon
- dense
- mqa
updated: '2026-11-23'
sources:
- url: https://huggingface.co/tiiuae/falcon-7b-instruct
  title: tiiuae/falcon-7b-instruct — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/tiiuae/falcon-7b-instruct/blob/main/config.json
  title: tiiuae/falcon-7b-instruct — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2205.14135
  title: arXiv:2205.14135
  trust: 0.95
- url: https://arxiv.org/abs/1911.02150
  title: arXiv:1911.02150
  trust: 0.95
- url: https://arxiv.org/abs/2005.14165
  title: arXiv:2005.14165
  trust: 0.95
- url: https://arxiv.org/abs/2104.09864
  title: arXiv:2104.09864
  trust: 0.95
- url: https://arxiv.org/abs/2306.01116
  title: arXiv:2306.01116
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/falcon/modeling_falcon.py
  title: 'transformers/models/falcon/modeling_falcon.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

Falcon-7B-Instruct is a chat model built by TII. The card says it is based on TII's own Falcon-7B model, which TII fine-tuned on a mixture of chat and instruction datasets so that it answers questions and follows requests. The card calls it a causal decoder-only model with 7 billion parameters, meaning it reads text and predicts the next word, and lists English and French as its languages.

## Intended Use

The card says it is for direct use as a chat or instruction model, fine-tuned on a mixture of instruct and chat datasets. It names production use without adequate assessment of risks and mitigation, and any irresponsible or harmful use, as out of scope, and advises users to build guardrails.

## What Sets It Apart

The card says the architecture is optimized for inference, using FlashAttention and multiquery attention, two techniques that make attention cheaper to compute and its memory smaller. It states the Falcon-7B base was trained on 1,500B tokens of RefinedWeb, a web-crawl dataset, plus curated corpora, and that the instruct version was fine-tuned on a mixture of about 250M tokens of chat and instruction data.

## Running It

The card says you need at least 16GB of memory to run inference swiftly and PyTorch 2.0 to use it with Transformers. It also says this instruct model may not be ideal for further fine-tuning, and suggests starting from Falcon-7B to build your own chat model.
