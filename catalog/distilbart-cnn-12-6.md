---
id: model/distilbart-cnn-12-6
type: model
slug: distilbart-cnn-12-6
schema_version: 1.1
title: distilbart-cnn-12-6
organization: organization/sshleifer
license: license/apache-2-0
model_category: language
tasks:
- task/summarization
context_window_tokens: 1024
summary: 'DistilBART CNN 12-6 is a summarization model: it reads a longer text and writes a shorter one. The card is brief. It presents the checkpoint as one of the DistilBART models, tags it English, lists the datasets cnn_dailymail and xsum, and compares it with bart-large-cnn as the baseline. It names no maker; the repository belongs to the sshleifer account on Hugging Face.'
what_sets_it_apart: The card's table lists the DistilBART variants beside the bart-large-cnn baseline, with their sizes and inference times. It shows distilbart-12-6-cnn at 306 million parameters against 406 million for the baseline, and 307 ms inference time against 381 ms. The card does not describe how the model was made.
running_it: The card says the checkpoint should be loaded with BartForConditionalGeneration.from_pretrained in the Transformers library.
fingerprint:
  num_layers: 12
  hidden_size: 1024
  context_window_tokens: 1024
  vocabulary_size: 50264
  activation_function: gelu
  norm_placement: post
  stack: encoder-decoder
filters:
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: summarization
  library: transformers
hub_signals:
  downloads_30d: 276369
  likes: 329
  last_modified: '2021-06-14'
  observed_at: '2026-11-21'
  source: https://huggingface.co/sshleifer/distilbart-cnn-12-6
aliases:
- sshleifer/distilbart-cnn-12-6
lineage:
  parents: []
  children: []
quotes:
  summary: Metrics for DistilBART models
  what_sets_it_apart: bart-large-cnn (baseline)
  running_it: This checkpoint should be loaded into `BartForConditionalGeneration.from_pretrained`.
tags:
- llm
- summarization
- bart
- dense
updated: '2026-11-23'
sources:
- url: https://huggingface.co/sshleifer/distilbart-cnn-12-6
  title: sshleifer/distilbart-cnn-12-6 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/sshleifer/distilbart-cnn-12-6/blob/main/config.json
  title: sshleifer/distilbart-cnn-12-6 — config.json
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bart/modeling_bart.py
  title: 'transformers/models/bart/modeling_bart.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---

## Summary

DistilBART CNN 12-6 is a summarization model: it reads a longer text and writes a shorter one. The card is brief. It presents the checkpoint as one of the DistilBART models, tags it English, lists the datasets cnn_dailymail and xsum, and compares it with bart-large-cnn as the baseline. It names no maker; the repository belongs to the sshleifer account on Hugging Face.

## What Sets It Apart

The card's table lists the DistilBART variants beside the bart-large-cnn baseline, with their sizes and inference times. It shows distilbart-12-6-cnn at 306 million parameters against 406 million for the baseline, and 307 ms inference time against 381 ms. The card does not describe how the model was made.

## Running It

The card says the checkpoint should be loaded with BartForConditionalGeneration.from_pretrained in the Transformers library.
