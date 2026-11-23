---
id: model/t5-base
type: model
slug: t5-base
schema_version: 1.1
title: t5-base
organization: organization/google-t5
license: license/apache-2-0
model_category: language
tasks:
- task/translation
parameters_active_b: 0.22
total_parameters: 222903936
context_window_tokens: 512
summary: T5 Base is the 220 million parameter checkpoint of T5, the Text-To-Text Transfer Transformer, a language model from Google researchers. T5 treats every language task as text in and text out. The card lists English, French, Romanian and German as its languages.
intended_use: Quoting the T5 developers, the card says the same model, loss function and hyperparameters can be used on any language task, including machine translation, document summarization, question answering and classification such as sentiment analysis. It gives no out-of-scope uses and says more information is needed.
what_sets_it_apart: The developers describe reframing all language tasks into one text-to-text format, in contrast to BERT-style models that output only a class label or a span of the input. The card says the model was pre-trained on the C4 corpus (Colossal Clean Crawled Corpus) in a multi-task mixture of unsupervised denoising and supervised tasks, such as sentiment analysis, paraphrasing, natural language inference and question answering.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  context_window_tokens: 512
  vocabulary_size: 32128
  normalization: LayerNorm
  norm_placement: pre
  stack: encoder-decoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: translation
  library: transformers
hub_signals:
  downloads_30d: 2850381
  likes: 811
  last_modified: '2024-02-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/google-t5/t5-base
aliases:
- google-t5/t5-base
lineage:
  parents: []
  children: []
quotes:
  summary: T5-Base is the checkpoint with 220 million parameters.
  intended_use: Our text-to-text framework allows us to use the same model, loss function, and hyperparameters on any NLP task, including machine translation, document summarization, question answering, and classification tasks (e.g., sentiment analysis).
  what_sets_it_apart: With T5, we propose reframing all NLP tasks into a unified text-to-text-format where the input and output are always text strings
tags:
- llm
- translation
- t5
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/google-t5/t5-base
  title: google-t5/t5-base — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/google-t5/t5-base/blob/main/config.json
  title: google-t5/t5-base — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1805.12471
  title: arXiv:1805.12471
  trust: 0.95
- url: https://arxiv.org/abs/1708.00055
  title: arXiv:1708.00055
  trust: 0.95
- url: https://arxiv.org/abs/1704.05426
  title: arXiv:1704.05426
  trust: 0.95
- url: https://arxiv.org/abs/1606.05250
  title: arXiv:1606.05250
  trust: 0.95
- url: https://arxiv.org/abs/1808.09121
  title: arXiv:1808.09121
  trust: 0.95
- url: https://arxiv.org/abs/1810.12885
  title: arXiv:1810.12885
  trust: 0.95
- url: https://arxiv.org/abs/1905.10044
  title: arXiv:1905.10044
  trust: 0.95
- url: https://arxiv.org/abs/1910.09700
  title: arXiv:1910.09700
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/t5/modeling_t5.py
  title: 'transformers/models/t5/modeling_t5.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

T5 Base is the 220 million parameter checkpoint of T5, the Text-To-Text Transfer Transformer, a language model from Google researchers. T5 treats every language task as text in and text out. The card lists English, French, Romanian and German as its languages.

## Intended Use

Quoting the T5 developers, the card says the same model, loss function and hyperparameters can be used on any language task, including machine translation, document summarization, question answering and classification such as sentiment analysis. It gives no out-of-scope uses and says more information is needed.

## What Sets It Apart

The developers describe reframing all language tasks into one text-to-text format, in contrast to BERT-style models that output only a class label or a span of the input. The card says the model was pre-trained on the C4 corpus (Colossal Clean Crawled Corpus) in a multi-task mixture of unsupervised denoising and supervised tasks, such as sentiment analysis, paraphrasing, natural language inference and question answering.
