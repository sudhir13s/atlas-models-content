---
id: hyperparameter/<slug>
type: hyperparameter
slug: <slug>
schema_version: 2.2
title: "<Human Readable Name>"
name: <exact_config_key>
context: training                # training | inference
priority: <int>                  # criticality rank within context; 1 = most impactful
importance: critical             # critical | important | optional
goals: [training, finetuning]
models: [llm]
range: "<lowest — highest of the typical_values below>"
controls: "<one plain-language sentence a newcomer understands: what the setting is>"
effects:                         # short phrases, drawn as the effect scale under the title
  decrease:
    - "<what changes when the value goes down>"
  increase:
    - "<what changes when the value goes up>"
scale:                           # only for a numeric setting; the axis of the effect scale
  min: <number>
  max: <number>
  typical_min: <number>          # the band the typical_values span
  typical_max: <number>
  spacing: linear                # linear | log (log needs min > 0); write decimals, not 2e-5
typical_values:                  # who used what; every row has its source
  - setting: "<who, for what>"
    value: "<value>"
    source: "<url of one entry of sources>"
mistakes:                        # 2 to 5, each traceable to a source or to the mechanism on the page
  - mistake: "<what people do>"
    why: "<why it goes wrong>"
    fix: "<what to do instead>"
visual:                          # optional second figure, computed by the site from these values
  kind: schedule                 # schedule | distribution; fields per kind in the schema
  peak: <number>
  warmup_steps: <int>
  total_steps: <int>
  curves: [constant, linear, cosine]
  marks: [<step of the worked example>]
  caption: "<what the figure shows, and where its numbers come from>"
tags: [<tag>]
updated: <YYYY-MM-DD>
related: [hyperparameter/<slug>]  # the settings this one interacts with
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
---

## Definition
<The precise statement, with every term explained in plain words at first use. An analogy is
welcome when it is accurate; mark it as an analogy.>

## How It Works
<The explanation of the second figure, which the page draws above this text: how to read it,
then the mechanism in plain words. Short paragraphs, bullets, small tables.>

## Calculation
<Say in words what is computed, then the formula (LaTeX), a table defining every symbol, and
numbered steps.>

## Worked Example
<Small numbers a reader can redo by hand. State where the starting numbers come from. Every step
numbered, showing the numbers substituted and the result. End with one line: "What this means".>

## What To Watch
<What to look at while using the setting, each point attributed to its source.>
