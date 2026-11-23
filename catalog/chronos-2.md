---
id: model/chronos-2
type: model
slug: chronos-2
schema_version: 1.1
title: chronos-2
organization: organization/amazon
release_date: '2025-10-30'
release_year: 2025
release_month: 10
license: license/apache-2-0
model_category: structured-data
tasks:
- task/time-series-forecasting
parameters_active_b: 0.12
total_parameters: 119477664
summary: Chronos-2 is a time series forecasting model from Amazon. A time series is a sequence of measurements over time, such as electricity prices, and a forecasting model predicts its future values. The card describes it as a 120-million-parameter model that forecasts without task-specific training, which is called zero-shot, and its table lists a maximum context length of 8192 and a maximum prediction length of 1024.
intended_use: The card presents it for zero-shot forecasting. One model handles a single series (univariate), several series at once (multivariate), and forecasts informed by extra related inputs called covariates, such as known future values. The card says it runs on both GPU and CPU.
what_sets_it_apart: The card describes an encoder-only design inspired by the T5 encoder that produces multi-step forecasts as quantiles, meaning a range of likely values rather than one number. A group attention mechanism lets the model learn from related series and covariates given in the input. The comparison table in the card lists cross-learning across items and multivariate forecasting as supported by Chronos-2 and not by Chronos-Bolt or the original Chronos. Training used real-world and large-scale synthetic datasets.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  vocabulary_size: 2
  activation_function: relu
  normalization: LayerNorm
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  attention_type: MHA
  is_gated: false
  pipeline_tag: time-series-forecasting
  library: chronos-forecasting
hub_signals:
  downloads_30d: 8542470
  likes: 578
  last_modified: '2026-06-05'
  observed_at: '2026-11-21'
  source: https://huggingface.co/amazon/chronos-2
aliases:
- amazon/chronos-2
lineage:
  parents: []
  children: []
quotes:
  summary: a 120M-parameter, encoder-only time series foundation model for zero-shot forecasting
  intended_use: tasks within a single architecture
  what_sets_it_apart: uses a group attention mechanism for efficient in-context learning across related series and covariates
tags:
- time-series-forecasting
- t5
- dense
- mha
updated: '2026-11-23'
sources:
- url: https://huggingface.co/amazon/chronos-2
  title: amazon/chronos-2 — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/amazon/chronos-2/blob/main/config.json
  title: amazon/chronos-2 — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2403.07815
  title: arXiv:2403.07815
  trust: 0.95
- url: https://arxiv.org/abs/2510.15821
  title: arXiv:2510.15821
  trust: 0.95
---

## Summary

Chronos-2 is a time series forecasting model from Amazon. A time series is a sequence of measurements over time, such as electricity prices, and a forecasting model predicts its future values. The card describes it as a 120-million-parameter model that forecasts without task-specific training, which is called zero-shot, and its table lists a maximum context length of 8192 and a maximum prediction length of 1024.

## Intended Use

The card presents it for zero-shot forecasting. One model handles a single series (univariate), several series at once (multivariate), and forecasts informed by extra related inputs called covariates, such as known future values. The card says it runs on both GPU and CPU.

## What Sets It Apart

The card describes an encoder-only design inspired by the T5 encoder that produces multi-step forecasts as quantiles, meaning a range of likely values rather than one number. A group attention mechanism lets the model learn from related series and covariates given in the input. The comparison table in the card lists cross-learning across items and multivariate forecasting as supported by Chronos-2 and not by Chronos-Bolt or the original Chronos. Training used real-world and large-scale synthetic datasets.
