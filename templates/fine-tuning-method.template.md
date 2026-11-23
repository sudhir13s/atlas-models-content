---
id: fine-tuning-method/<slug>
type: fine-tuning-method
slug: <slug>
schema_version: 3.1              # the schema's current version; the schema refuses any other
title: "<Method Name>"
method_type: peft                # peft | full | rlhf | preference | distillation
memory_profile: low             # low | medium | high
summary: "<one plain sentence: what the method does>"
when_to_use: "<one sentence; the When To Use section explains it in full>"
related_hyperparameters: [hyperparameter/<slug>]
profile:                         # this method's row of the comparison across all methods
  trains: "<which weights receive updates>"
  models_in_training: "<the models held while training>"
  data: "<the data it learns from>"
  stages: "<the training stages it needs>"
  source: "<url of one entry of sources>"
typical_values:                  # settings a cited source used, one row each
  - setting: "<who used it, and for what>"
    value: "<value>"
    source: "<url of one entry of sources>"
mistakes:                        # two to five
  - mistake: "<the mistake>"
    why: "<why it goes wrong, with the source's finding>"
    fix: "<what to do instead>"
visual:                          # one of: memory_terms, adapter_size, memory_bars, dpo_loss, orpo_loss, kl_reward
  kind: dpo_loss
  beta: 0.1
  policy: { chosen: -18.0, rejected: -21.0 }
  reference: { chosen: -20.0, rejected: -18.0 }
  caption: "<what is drawn; say so when the numbers are an example>"
tags: [<tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.95 }
---

## Definition

<Under the figure: the method in plain words, each term explained at first use.>

## How It Works

<Numbered steps of what is trained, what is frozen and what the loss or update is.>

## Calculation

<The method's formula, said in words first, every symbol in a table.>

## Worked Example

<Small numbers a reader can redo by hand, every step numbered with its result; the figure's
inputs are these numbers. Close with "What this means".>

## Memory

<What is held in memory while training, as a sum of sourced terms.>

## Trade-offs

<Bold lead line, then labelled bullets, each finding attributed to its source.>

## When To Use

<When the sources support using it, and when not.>
