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
pipeline:
  source: https://arxiv.org/abs/2510.15821
  summary: A group of related time series, the targets and any covariates, is scaled, cut into patches of 16 steps and embedded. An encoder-only transformer alternates attention along time within each series with attention across the series of the group, and a head turns the embeddings of the future patches into 21 quantiles per step.
  stages:
  - id: series
    role: input
    title: Time series group
    details:
    - targets + covariates
    - context up to 8,192 steps
    note: A group can be one series, several related series, the variates of a multivariate series, or targets with their covariates. Future values that are not known are marked as missing.
  - id: scaling
    role: normalisation
    title: Robust scaling
    details:
    - standardise, then arcsinh
    note: Each series is standardised with its own mean and spread, and the inverse hyperbolic sine is applied to soften extreme values.
  - id: patches
    role: tokenizer
    title: Patches + meta features
    details:
    - 16 steps per patch
    - time index and mask added
    note: A time index and a mask (1 where a value is observed) are appended, and context and future are each split into non-overlapping patches. A special REG token separates the context patches from the future patches.
  - id: patch-embedding
    role: embedding
    title: Patch embedding
    details:
    - residual network · width 768
    note: A small residual network maps each patch, with its time index and mask, to one vector.
  - id: time-attention
    role: attention
    title: Time attention
    details:
    - 12 heads · RoPE positions
    frame: Encoder block
    repeat: 12
    note: Self-attention along time, across the patches of one series. Positions use rotary position embeddings (RoPE).
  - id: group-attention
    role: attention
    title: Group attention
    details:
    - across the series of a group
    frame: Encoder block
    note: At each patch position, attention runs across all series of the same group and never across groups. It uses no position information, since the series have no natural order.
  - id: feed-forward
    role: feedForward
    title: Feed-forward
    details:
    - width 3072 · ReLU
    frame: Encoder block
  - id: norm
    role: normalisation
    title: Final LayerNorm
  - id: quantile-head
    role: outputHead
    title: Quantile head
    details:
    - residual block · 21 quantiles
    - 16 steps per future patch
    note: The embeddings of the future patches of each target become forecasts for several patches in one pass, at the quantiles 0.01 to 0.99. The scaling is then undone.
  - id: forecast
    role: outputHead
    title: Quantile forecast
    details:
    - up to 1,024 steps ahead
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
- url: https://github.com/amazon-science/chronos-forecasting/blob/main/src/chronos/chronos2/model.py
  title: 'chronos-forecasting/src/chronos/chronos2/model.py: Chronos-2 model code (Amazon)'
  trust: 0.9
---

## Summary

Chronos-2 is a time series forecasting model from Amazon. A time series is a sequence of measurements over time, such as electricity prices, and a forecasting model predicts its future values. The card describes it as a 120-million-parameter model that forecasts without task-specific training, which is called zero-shot, and its table lists a maximum context length of 8192 and a maximum prediction length of 1024.

## Intended Use

The card presents it for zero-shot forecasting. One model handles a single series (univariate), several series at once (multivariate), and forecasts informed by extra related inputs called covariates, such as known future values. The card says it runs on both GPU and CPU.

## What Sets It Apart

The card describes an encoder-only design inspired by the T5 encoder that produces multi-step forecasts as quantiles, meaning a range of likely values rather than one number. A group attention mechanism lets the model learn from related series and covariates given in the input. The comparison table in the card lists cross-learning across items and multivariate forecasting as supported by Chronos-2 and not by Chronos-Bolt or the original Chronos. Training used real-world and large-scale synthetic datasets.
