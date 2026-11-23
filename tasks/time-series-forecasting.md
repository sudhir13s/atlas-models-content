---
id: task/time-series-forecasting
type: task
slug: time-series-forecasting
schema_version: 3.1
title: Time-Series Forecasting
group: task-group/structured-data
order: 3
summary: Predict the future values of a measurement from its own history.
hf_pipeline_tags:
- time-series-forecasting
inputs:
- time-series
outputs:
- time-series
tags:
- time-series
- forecasting
- structured-data
updated: '2026-11-18'
sources:
- url: https://huggingface.co/models?pipeline_tag=time-series-forecasting
  title: Hugging Face — models tagged time-series-forecasting
  trust: 0.9
---

## What It Is

A forecasting model reads a sequence of past values and **continues it into the future**.

- **Pretrained forecasting models:** trained on many series, they forecast a new one with no training on it.
- **Classical statistical methods:** fitted to each series separately, they model trend and seasonality directly.
- **Probabilistic output:** many models return a range of likely values, not a single line.

## When To Use

- **Use it** for demand, load, traffic, sensor readings and other values recorded at regular intervals.
- **Use tabular regression instead** when rows are independent and order in time does not matter.
- **Use tabular classification instead** when you want to label an event, not predict a future value.

## How To Choose

- **Pretrained or classical:** pretrained models save setup; classical methods stay strong on short, regular series.
- **Forecast horizon and context length:** how far ahead the model predicts and how much history it reads.
- **Extra inputs:** whether known future factors or related series can be supplied alongside the history.
- **Uncertainty:** whether prediction intervals are provided and how reliable they are.
