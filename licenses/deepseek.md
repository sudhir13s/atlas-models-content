---
id: license/deepseek
type: license
slug: deepseek
schema_version: 3.1
title: DeepSeek Model License
name: DeepSeek License Agreement
spdx_id: DeepSeek
category: custom
url: https://github.com/deepseek-ai/DeepSeek-V3/blob/main/LICENSE-MODEL
permissions:
  commercial_use: true
  modification: true
  distribution: true
  fine_tuning: true
  output_training: true
  patent_grant: true
  private_use: true
conditions:
- Give every recipient of the model or a derivative a copy of the license
- Carry the use-based restrictions through to all downstream users and derivatives
- Mark modified files with a prominent notice of the change
- Retain copyright, patent, trademark and attribution notices
limitations:
- Attachment A forbids uses such as breaking the law, any military use, harming minors, spreading verifiably false information to harm others, defamation, fully automated decisions that adversely affect legal rights and discrimination
- Licensee must impose the same use restrictions on downstream recipients
- DeepSeek may restrict use that violates the license, remotely or otherwise
- The patent license ends if you sue claiming the model infringes a patent
- No trademark rights
- No warranty
- No liability
summary: 'Custom DeepSeek license: commercial use, modification, fine-tuning and distribution are all permitted with a copyright and patent license, subject to a use-based restrictions list that must flow through to downstream users.'
tags:
- license
- custom
- deepseek
- open-weight
updated: '2026-11-24'
sources:
- url: https://github.com/deepseek-ai/DeepSeek-V3/blob/main/LICENSE-MODEL
  title: DeepSeek Model License (DeepSeek-V3)
  trust: 0.95
- url: https://github.com/deepseek-ai/DeepSeek-V3/blob/main/LICENSE-CODE
  title: DeepSeek-V3 code license (MIT)
  trust: 0.95
---

## Plain-English

The DeepSeek License Agreement (Version 1.0) is a custom "open and responsible" license that governs
DeepSeek's model weights (in the DeepSeek-V3 repository the *code* is MIT, and the weights carry this
separate agreement). You may use the model commercially, modify it, fine-tune it, distribute it and
its derivatives, host it as a service, and train other models on its outputs; such distilled models
count as derivatives. What makes it custom rather than a standard open-source license is a set of
**use-based restrictions** — Attachment A, which forbids uses such as unlawful, military,
discriminatory or harmful applications — that you must both comply with and **include, at minimum,
in the terms for anyone you distribute the model or a derivative to**. In other words, you cannot
license the restrictions away. It grants an explicit patent license, which ends if you sue over
patents in the model, and comes with no warranty. Practically, for a lawful commercial deployment
this behaves like an open-weight license with an enforceable acceptable-use policy attached.
