---
id: architecture-era/mixture-of-experts
type: architecture-era
slug: mixture-of-experts
schema_version: 1.0
title: "Mixture of Experts (Sparse MoE)"
name: "Mixture of Experts"
era: post-transformer
era_range: "2021–2026"
year: 2024
family: llm
core_innovation: "Replace the dense feed-forward block with many experts and a router that activates only a few per token."
description: "Sparse transformers where total parameters greatly exceed the parameters active per token."
key_features: ["Expert feed-forward blocks", "Top-k token routing", "Sparse activation", "Total ≫ active parameters"]
key_models: ["Switch Transformer (2021)", "Mixtral 8x7B (2024)", "DeepSeek-V3 (2024)"]
paper_title: "Mixtral of Experts"
paper_url: "https://arxiv.org/abs/2401.04088"
influenced_by: [architecture-era/transformer]
led_to: []
tags: [moe, sparse, routing, llm]
updated: 2026-07-16
sources:
  - { url: "https://arxiv.org/abs/2401.04088", title: "Mixtral of Experts (Jiang et al.)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2101.03961", title: "Switch Transformers (Fedus et al.)", trust: 0.9 }
---

## What Changed
Mixture-of-Experts keeps the transformer backbone but replaces each dense feed-forward block with a
set of N expert FFNs plus a lightweight router. For every token the router selects the top-k experts
(often k=2), so only a fraction of the network's parameters are active per token. This decouples
model capacity (total parameters) from inference cost (active parameters): Mixtral 8x7B has ~47B
total parameters but activates ~13B per token. It descends from the dense transformer and the
Switch Transformer's routing, and underpins several modern frontier models.

## Key Ideas
- **Experts** — multiple parallel FFNs; capacity scales with expert count.
- **Router** — a small gating network scores experts per token and picks the top-k.
- **Sparse activation** — compute stays close to a dense model of the *active* size, while quality
  tracks the much larger *total* size.
- **Load balancing** — an auxiliary loss spreads tokens across experts so none collapse or starve.
