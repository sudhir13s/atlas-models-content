---
id: parameter/output-head
type: parameter
slug: output-head
schema_version: 1.0
title: "Output / Unembedding Head"
name: lm_head
priority: 9
importance: important
applies_to: [llm, transformer, moe]
range: "0 (if tied) or vocab_size × hidden_size"
controls: "The final linear layer that projects the last hidden state back to a logit for every token in the vocabulary."
formula: "output head params = vocab_size × hidden_size (0 if tied to the embedding)"
if_increased: "An untied head (separate from the embedding) gives the model an independent output representation, at the cost of another V×d matrix."
if_decreased: "Tying the head to the embedding removes V×d parameters entirely, at a small quality cost for large models."
tags: [llm, transformer, output, lm-head, unembedding, parameters]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1608.05859", title: "Using the Output Embedding to Improve Language Models (Press & Wolf, 2017)", trust: 0.85 }
  - { url: "https://arxiv.org/abs/2407.21783", title: "The Llama 3 Herd of Models", trust: 0.9 }
---

## Definition
The **output head** (`lm_head`, or unembedding matrix) is the final `hidden_size × vocab_size`
projection that turns the model's last hidden vector into a **logit per token**. A softmax over those
logits gives the next-token distribution.

Structurally it is the mirror of the token embedding: embedding maps ID → vector, the head maps
vector → scores over all IDs.

## What to look for
The key question is **tied vs untied**:

- **Tied** — the head reuses the (transposed) embedding matrix, adding **zero** new parameters.
- **Untied** — a separate matrix, `vocab_size × hidden_size`, giving the model output freedom the
  input embedding does not have to serve.

Large models (Llama 3, GPT-3) often keep them **untied**; small models tie them to avoid spending a
big fraction of the budget twice.

## Calculation
$$\text{output head} = \begin{cases} 0 & \text{tied} \\ V \times d & \text{untied} \end{cases}$$

## Example
Llama 3 8B keeps embeddings **untied**, so the output head is a full `128256 × 4096 ≈ 525.3M`
parameters — the same size as the embedding table. Tying them would save that 525M (≈6.5% of the
model) but is skipped at this scale.
