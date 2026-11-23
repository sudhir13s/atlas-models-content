---
id: model/distilbert-base-cased-distilled-squad
type: model
slug: distilbert-base-cased-distilled-squad
schema_version: 1.1
title: distilbert-base-cased-distilled-squad
organization: organization/distilbert
license: license/apache-2-0
model_category: language
tasks:
- task/question-answering
parameters_active_b: 0.07
total_parameters: 65192450
context_window_tokens: 512
summary: This model is a question-answering version of DistilBERT, a small language model that Hugging Face made by distilling (teaching a smaller model to imitate) BERT. Given a question and a passage, it picks out the part of the passage that answers the question. The card says it is a fine-tuned checkpoint of DistilBERT-base-cased, trained with a second step of knowledge distillation on the SQuAD v1.1 dataset, and works in English.
intended_use: 'The card says the model can be used for question answering. It lists out-of-scope uses: creating hostile or alienating environments for people, and generating content presented as factual, since the model was not trained to be factual or true.'
what_sets_it_apart: The card describes DistilBERT as a small, fast, cheap and light Transformer trained by distilling BERT base. It says DistilBERT has 40% fewer parameters than bert-base-uncased and runs 60% faster while preserving over 95% of BERT's performance as measured on the GLUE language-understanding benchmark.
fingerprint:
  attention_type: MHA
  num_layers: 6
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  context_window_tokens: 512
  vocabulary_size: 28996
  norm_placement: post
  stack: encoder
  embedding_norm: true
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: question-answering
  library: transformers
hub_signals:
  downloads_30d: 154153
  likes: 279
  last_modified: '2024-05-06'
  observed_at: '2026-11-21'
  source: https://huggingface.co/distilbert/distilbert-base-cased-distilled-squad
aliases:
- distilbert/distilbert-base-cased-distilled-squad
lineage:
  parents: []
  children: []
quotes:
  summary: DistilBERT is a small, fast, cheap and light Transformer model trained by distilling BERT base.
  intended_use: This model can be used for question answering.
  what_sets_it_apart: runs 60% faster while preserving over 95% of BERT's performances as measured on the GLUE language understanding benchmark.
tags:
- llm
- question-answering
- distilbert
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/distilbert/distilbert-base-cased-distilled-squad
  title: distilbert/distilbert-base-cased-distilled-squad — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/distilbert/distilbert-base-cased-distilled-squad/blob/main/config.json
  title: distilbert/distilbert-base-cased-distilled-squad — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.01108
  title: arXiv:1910.01108
  trust: 0.95
- url: https://arxiv.org/abs/1910.09700
  title: arXiv:1910.09700
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/distilbert/modeling_distilbert.py
  title: 'transformers/models/distilbert/modeling_distilbert.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

This model is a question-answering version of DistilBERT, a small language model that Hugging Face made by distilling (teaching a smaller model to imitate) BERT. Given a question and a passage, it picks out the part of the passage that answers the question. The card says it is a fine-tuned checkpoint of DistilBERT-base-cased, trained with a second step of knowledge distillation on the SQuAD v1.1 dataset, and works in English.

## Intended Use

The card says the model can be used for question answering. It lists out-of-scope uses: creating hostile or alienating environments for people, and generating content presented as factual, since the model was not trained to be factual or true.

## What Sets It Apart

The card describes DistilBERT as a small, fast, cheap and light Transformer trained by distilling BERT base. It says DistilBERT has 40% fewer parameters than bert-base-uncased and runs 60% faster while preserving over 95% of BERT's performance as measured on the GLUE language-understanding benchmark.
