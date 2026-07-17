---
id: license/qwen
type: license
slug: qwen
schema_version: 1.0
title: "Qwen License Agreement"
name: "Qwen License Agreement (Tongyi Qianwen)"
spdx_id: Qwen
category: custom
url: "https://huggingface.co/Qwen/Qwen2.5-72B-Instruct/blob/main/LICENSE"
permissions:
  commercial_use: true
  modification: true
  distribution: true
  fine_tuning: true
  output_training: true
  patent_grant: false
  private_use: true
conditions: ["Retain copyright and attribution notices", "Include a copy of the Qwen license with redistributions", "Mark any files you modified as changed", "Note that derivatives are built on Qwen where required"]
limitations: ["A separate commercial license from Alibaba is required if the product/service exceeds 100 million monthly active users", "Acceptable-use restrictions apply", "No trademark rights beyond descriptive use", "No warranty", "No liability"]
models_using: [model/qwen2-5-72b-instruct]
summary: "Custom Alibaba license: commercially usable and permissive in practice, but a separate license from Alibaba is required above 100 million monthly active users."
tags: [license, custom, qwen, alibaba, open-weight]
updated: 2026-07-17
sources:
  - { url: "https://huggingface.co/Qwen/Qwen2.5-72B-Instruct/blob/main/LICENSE", title: "Qwen LICENSE (Qwen2.5-72B-Instruct)", trust: 0.95 }
---

## Plain-English
The Qwen License Agreement (historically "Tongyi Qianwen") is Alibaba's custom license used on some of
the larger Qwen checkpoints — including Qwen2.5-72B-Instruct — while many smaller Qwen models ship
under plain Apache-2.0. For almost everyone it behaves like a permissive open-weight license: you can
use it commercially, modify it, fine-tune it, distribute it, and train on its outputs, as long as you
keep the attribution and license notices and mark any changes you make. The one meaningful catch is a
**scale threshold**: if the product or service built on the model has more than **100 million monthly
active users**, you must request a separate commercial license from Alibaba. Standard acceptable-use
restrictions apply, there is no patent grant, and the weights come with no warranty. Think of it as
"open weight, commercially friendly, with a large-scale carve-out" rather than fully open source.
