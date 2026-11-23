---
id: model/bart-large-cnn
type: model
slug: bart-large-cnn
schema_version: 1.1
title: bart-large-cnn
organization: organization/meta
license: license/mit
model_category: language
tasks:
- task/summarization
parameters_active_b: 0.41
total_parameters: 406290432
context_window_tokens: 1024
summary: 'BART Large CNN is a text summarization model: given a news article, it writes a shorter version. It is published under the facebook organization on Hugging Face as an English BART model, a design that pairs a reading half (encoder) with a writing half (decoder), fine-tuned on the CNN Daily Mail collection of articles and their summaries. The card was written by the Hugging Face team, not by the team that released BART.'
intended_use: The intended-use section of the card says only that the model can be used for text summarization. The card describes BART in general as also effective, once fine-tuned, for translation, text classification and question answering, but this checkpoint is the one fine-tuned on CNN Daily Mail.
what_sets_it_apart: The card describes BART as a sequence-to-sequence transformer with a bidirectional encoder, like BERT, and an autoregressive decoder, like GPT, which writes one token at a time from left to right. In pre-training the text is first corrupted with a noising function and the model learns to reconstruct the original. This checkpoint was then fine-tuned on CNN Daily Mail.
fingerprint:
  attention_type: MHA
  num_layers: 12
  decoder_layers: 12
  attention_heads: 16
  kv_heads: 16
  head_size: 64
  hidden_size: 1024
  intermediate_size: 4096
  context_window_tokens: 1024
  vocabulary_size: 50264
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: post
  stack: encoder-decoder
  embedding_norm: true
  positional_encoding: Learned
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: summarization
  library: transformers
hub_signals:
  downloads_30d: 1052842
  likes: 1654
  last_modified: '2024-02-13'
  observed_at: '2026-11-21'
  source: https://huggingface.co/facebook/bart-large-cnn
aliases:
- facebook/bart-large-cnn
lineage:
  parents: []
  children: []
quotes:
  summary: BART model pre-trained on English language, and fine-tuned on
  intended_use: You can use this model for text summarization.
  what_sets_it_apart: a bidirectional (BERT-like) encoder and an autoregressive (GPT-like) decoder
tags:
- llm
- summarization
- bart
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/facebook/bart-large-cnn
  title: facebook/bart-large-cnn — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/facebook/bart-large-cnn/blob/main/config.json
  title: facebook/bart-large-cnn — config.json
  trust: 0.9
- url: https://arxiv.org/abs/1910.13461
  title: arXiv:1910.13461
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bart/modeling_bart.py
  title: 'transformers/models/bart/modeling_bart.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

BART Large CNN is a text summarization model: given a news article, it writes a shorter version. It is published under the facebook organization on Hugging Face as an English BART model, a design that pairs a reading half (encoder) with a writing half (decoder), fine-tuned on the CNN Daily Mail collection of articles and their summaries. The card was written by the Hugging Face team, not by the team that released BART.

## Intended Use

The intended-use section of the card says only that the model can be used for text summarization. The card describes BART in general as also effective, once fine-tuned, for translation, text classification and question answering, but this checkpoint is the one fine-tuned on CNN Daily Mail.

## What Sets It Apart

The card describes BART as a sequence-to-sequence transformer with a bidirectional encoder, like BERT, and an autoregressive decoder, like GPT, which writes one token at a time from left to right. In pre-training the text is first corrupted with a noising function and the model learns to reconstruct the original. This checkpoint was then fine-tuned on CNN Daily Mail.
