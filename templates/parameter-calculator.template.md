---
id: parameter-calculator/<slug>
type: parameter-calculator
slug: <slug>
schema_version: 3.1              # the schema's current version; the schema refuses any other
title: "<Architecture Family> Parameters"
arch: llm                        # llm | cnn | rnn | gan | diffusion | moe | ssm | mlp
order: <int>
color_key: llm
components:                      # structural knobs, ranked (rank 1 = most impactful)
  - { name: "<knob>", rank: 1, importance: critical, badge: "Per Layer", formula: "<formula>", range: "<range>" }
worked_example:
  label: "<Model Name>"
  config: { hidden_size: <int>, num_layers: <int>, attention_heads: <int>, kv_heads: <int>, intermediate_size: <int>, vocabulary_size: <int>, head_size: <int>, activation_function: SwiGLU, normalization: RMSNorm, tiedEmbeddings: false }   # a transformer fact takes the fingerprint's name
  sources:
    - { url: "<url>", title: "<title>", trust: 0.9 }
tags: [<tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
---

## Overview
<What these parameters are and how they combine into the model's capacity.>

## Components
<One subsection per component with its formula and what to look for.>

## Worked Example
<Plug the worked_example.config into the master formula and show the total.>
