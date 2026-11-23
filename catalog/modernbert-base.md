---
id: model/modernbert-base
type: model
slug: modernbert-base
schema_version: 1.1
title: ModernBERT-base
organization: organization/answerdotai
release_date: '2024-12-11'
release_year: 2024
release_month: 12
license: license/apache-2-0
model_category: language
tasks:
- task/fill-mask
parameters_active_b: 0.15
total_parameters: 149655232
context_window_tokens: 8192
summary: ModernBERT-base is a BERT-style language model, an encoder that reads text in both directions and produces representations of it, published by Answer.AI and LightOn with others. It is the smaller of two sizes, with 22 layers and 149 million parameters. The card says it was pre-trained on 2 trillion tokens of English text and code and handles up to 8,192 tokens natively.
intended_use: The card says the model is trained as a masked language model, which predicts hidden words in a sentence, so it can be used directly for filling in masked words. For downstream tasks such as classification, retrieval or question answering, it advises fine-tuning with standard BERT recipes. It names long-document tasks, code retrieval and hybrid text-and-code search as suited to its long context and training data.
what_sets_it_apart: The card lists Rotary Positional Embeddings for long-context support, alternating local and global attention for efficiency on long inputs, and unpadding with Flash Attention for efficient inference. It describes training on sequences of up to 1,024 tokens first, then extending to 8,192 tokens.
limitations: The card says that because the training data is mostly English and code, performance may be lower in other languages. Using the full 8,192-token window may be slower than short inputs, and the representations may reflect biases in the training data.
fingerprint:
  attention_type: MHA
  num_layers: 22
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 1152
  context_window_tokens: 8192
  vocabulary_size: 50368
  normalization: LayerNorm
  norm_placement: pre
  stack: encoder
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: fill-mask
  library: transformers
hub_signals:
  downloads_30d: 2780362
  likes: 1159
  last_modified: '2025-01-15'
  observed_at: '2026-11-21'
  source: https://huggingface.co/answerdotai/ModernBERT-base
aliases:
- answerdotai/ModernBERT-base
lineage:
  parents: []
  children: []
quotes:
  summary: ModernBERT is a modernized bidirectional encoder-only Transformer model (BERT-style) pre-trained on 2 trillion tokens of English and code data with a native context length of up to 8,192 tokens.
  intended_use: To use ModernBERT for downstream tasks like classification, retrieval, or QA, fine-tune it following standard BERT fine-tuning recipes.
  what_sets_it_apart: 'Sequence Length: Pre-trained up to 1,024 tokens, then extended to 8,192 tokens.'
  limitations: ModernBERT’s training data is primarily English and code, so performance may be lower for other languages.
tags:
- llm
- fill-mask
- modernbert
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/answerdotai/ModernBERT-base
  title: answerdotai/ModernBERT-base — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/answerdotai/ModernBERT-base/blob/main/config.json
  title: answerdotai/ModernBERT-base — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2412.13663
  title: arXiv:2412.13663
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/modernbert/modeling_modernbert.py
  title: 'transformers/models/modernbert/modeling_modernbert.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

ModernBERT-base is a BERT-style language model, an encoder that reads text in both directions and produces representations of it, published by Answer.AI and LightOn with others. It is the smaller of two sizes, with 22 layers and 149 million parameters. The card says it was pre-trained on 2 trillion tokens of English text and code and handles up to 8,192 tokens natively.

## Intended Use

The card says the model is trained as a masked language model, which predicts hidden words in a sentence, so it can be used directly for filling in masked words. For downstream tasks such as classification, retrieval or question answering, it advises fine-tuning with standard BERT recipes. It names long-document tasks, code retrieval and hybrid text-and-code search as suited to its long context and training data.

## What Sets It Apart

The card lists Rotary Positional Embeddings for long-context support, alternating local and global attention for efficiency on long inputs, and unpadding with Flash Attention for efficient inference. It describes training on sequences of up to 1,024 tokens first, then extending to 8,192 tokens.

## Limitations

The card says that because the training data is mostly English and code, performance may be lower in other languages. Using the full 8,192-token window may be slower than short inputs, and the representations may reflect biases in the training data.
