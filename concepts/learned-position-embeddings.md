---
id: concept/learned-position-embeddings
type: concept
slug: learned-position-embeddings
schema_version: 1.2
title: Learned Position Embeddings
category: positional-encoding
code: Learned
aliases: []
order: 3
short_description: A trainable vector for each position — the original approach.
example_models:
- GPT-2
- BERT
- OPT
- Pythia
tags:
- positional-encoding
- learned
updated: '2026-11-19'
sources: []
---

## What It Is

Each position (1, 2, 3, ... up to max_length) gets its own learned embedding vector that is added to the token embedding. The model learns during training what each absolute position means. This is the original GPT/BERT approach.

## Why It Matters

Simple to implement, works well within the trained context length. The model directly optimizes positional representations during training.

## Trade-off

Cannot generalize beyond the trained context length at all — position 4097 literally has no embedding if you trained with max 4096. No relative position awareness: the model must learn that position 10→11 is 'adjacent' the same way as 500→501.
