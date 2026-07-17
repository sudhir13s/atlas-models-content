---
id: license/deepseek
type: license
slug: deepseek
schema_version: 1.0
title: "DeepSeek Model License"
name: "DeepSeek Model License"
spdx_id: DeepSeek
category: custom
url: "https://github.com/deepseek-ai/DeepSeek-V3/blob/main/LICENSE-MODEL"
permissions:
  commercial_use: true
  modification: true
  distribution: true
  fine_tuning: true
  output_training: true
  patent_grant: false
  private_use: true
conditions: ["Include a copy of the DeepSeek Model License with any distribution", "Carry the use-based restrictions through to all downstream users and derivatives", "Retain attribution and copyright notices"]
limitations: ["Use-based restrictions apply (the license's prohibited-use list, e.g. no unlawful, harmful, or deceptive uses)", "Licensee must impose the same use restrictions on downstream recipients", "No warranty", "No liability"]
models_using: [model/deepseek-v3]
summary: "Custom DeepSeek license: commercial use, modification, fine-tuning and distribution are all permitted, subject to a use-based restrictions list that must flow through to downstream users."
tags: [license, custom, deepseek, open-weight]
updated: 2026-07-17
sources:
  - { url: "https://github.com/deepseek-ai/DeepSeek-V3/blob/main/LICENSE-MODEL", title: "DeepSeek Model License (DeepSeek-V3)", trust: 0.95 }
---

## Plain-English
The DeepSeek Model License is a custom "open and responsible" license that governs DeepSeek's model
weights (the repository *code* is typically MIT, but the weights carry this separate agreement). It is
commercially friendly: you may use the model commercially, modify it, fine-tune it, distribute it and
its derivatives, and train other models on its outputs. What makes it custom rather than a standard
open-source license is a set of **use-based restrictions** — an appendix listing prohibited uses such
as unlawful, harmful, discriminatory, or deceptive applications — that you must both comply with and
**pass through unchanged to anyone you distribute the model to**. In other words, you cannot use the
restrictions away by relicensing. There is no explicit patent grant and no warranty. Practically, for a
lawful commercial deployment this behaves like an open-weight license with an enforceable
acceptable-use policy attached.
