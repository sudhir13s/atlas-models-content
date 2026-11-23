---
id: task/tabular-regression
type: task
slug: tabular-regression
schema_version: 3.1
title: Tabular Regression
group: task-group/structured-data
order: 2
summary: Predict a number for each row of a table from the values in its columns.
hf_pipeline_tags:
- tabular-regression
inputs:
- tabular
outputs:
- score
tags:
- tabular
- regression
- structured-data
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/tabular-regression
  title: Hugging Face — Tabular Regression task
  trust: 0.9
---

## What It Is

A tabular regression model reads one row of a table and **outputs a continuous value**.

- **Numeric targets:** typical examples are a price, a duration, a demand level or a risk score.
- **Same model families as classification:** linear models, gradient-boosted trees and neural networks.
- **Trees are the usual starting point:** they are strong on most tables and need little preparation.

## When To Use

- **Use it** when the answer is a quantity and each row can be predicted on its own.
- **Use tabular classification instead** when the target is a category, even one encoded as a number.
- **Use time-series forecasting instead** when the value depends mainly on its own past.

## How To Choose

- **Error measure:** pick the metric that matches the cost of being wrong, such as absolute or squared error.
- **Uncertainty:** whether the model returns a range or only a single number.
- **Behaviour outside the training range:** trees cannot extrapolate beyond values they have seen.
- **Table size and column types:** these decide between pretrained models, trees and linear models.
