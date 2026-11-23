---
id: model/llama-3-2-1b-instruct
type: model
slug: llama-3-2-1b-instruct
schema_version: 3.0
title: Llama-3.2-1B-Instruct
organization: organization/meta
release_date: '2024-09-18'
license: license/llama-3-2
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
total_parameters: 1235814400
summary: Llama-3.2-1B-Instruct is a small text-only chat model from Meta, the smallest instruction-tuned model of the Llama 3.2 collection, which comes in 1B and 3B sizes. It reads text and writes text in reply, and the card gives it 1.23 billion parameters and a context length of 128k tokens. English, German, French, Italian, Portuguese, Hindi, Spanish and Thai are the languages the card lists as officially supported.
intended_use: Meta says Llama 3.2 is meant for commercial and research use in multiple languages, and that the instruction-tuned text models are intended for assistant-like chat and agentic applications such as knowledge retrieval, summarization, mobile writing assistants, and query and prompt rewriting. Out of scope are uses that break applicable laws or the Acceptable Use Policy and licence, and uses in languages beyond the supported ones.
what_sets_it_apart: The card describes an auto-regressive language model with an optimized transformer architecture, where the tuned versions use supervised fine-tuning (SFT) and reinforcement learning with human feedback (RLHF) to align with human preferences for helpfulness and safety. It lists Grouped-Query Attention (GQA), a way of sharing attention keys and values to speed up inference, and shared embeddings for all Llama 3.2 text models.
filters:
  size_category: small
  is_gated: true
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 1313379
  likes: 2007
  last_modified: '2024-10-24'
  observed_at: '2026-11-20'
  source: https://huggingface.co/meta-llama/Llama-3.2-1B-Instruct
variants:
- name: RedHatAI/Llama-3.2-1B-Instruct-FP8
  format: fp8
  address: https://huggingface.co/RedHatAI/Llama-3.2-1B-Instruct-FP8
  publisher: RedHatAI
- name: RedHatAI/Llama-3.2-1B-Instruct-FP8-dynamic
  format: fp8
  address: https://huggingface.co/RedHatAI/Llama-3.2-1B-Instruct-FP8-dynamic
  publisher: RedHatAI
aliases:
- meta-llama/Llama-3.2-1B-Instruct
- llama-3.2-1b-instruct
- llama-3.2-1b-instruct-fp8
- llama-3.2-1b-instruct-fp8-dynamic
lineage:
  parents: []
  children: []
quotes:
  summary: The Llama 3.2 collection of multilingual large language models (LLMs) is a collection of pretrained and instruction-tuned generative models in 1B and 3B sizes (text in/text out).
  intended_use: Instruction tuned text only models are intended for assistant-like chat and agentic applications like knowledge retrieval and summarization, mobile AI powered writing assistants and query and prompt rewriting.
  what_sets_it_apart: The tuned versions use supervised fine-tuning (SFT) and reinforcement learning with human feedback (RLHF) to align with human preferences for helpfulness and safety.
tags: []
updated: '2026-11-23'
sources:
- url: https://huggingface.co/meta-llama/Llama-3.2-1B-Instruct
  title: meta-llama/Llama-3.2-1B-Instruct — model page (Hugging Face)
  trust: 0.9
- url: https://arxiv.org/abs/2204.05149
  title: arXiv:2204.05149
  trust: 0.95
- url: https://arxiv.org/abs/2405.16406
  title: arXiv:2405.16406
  trust: 0.95
---


