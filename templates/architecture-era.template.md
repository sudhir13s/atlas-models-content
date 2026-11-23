---
id: architecture-era/<slug>
type: architecture-era
slug: <slug>
schema_version: 3.1              # the schema's current version; the schema refuses any other
title: "<Human Readable Name>"
name: "<Name>"
era: scaling                     # early | foundation | scaling | post-transformer
era_range: "<start–end>"
year: <YYYY>
family: llm                      # classical-nn | classical-dl | llm | vision | generation | multimodal | other
core_innovation: "<the one thing it introduced>"
description: "<one-line description>"
key_features: ["<feature>", "<feature>"]
paper_title: "<Paper Title>"
paper_url: "<arxiv url>"
influenced_by: [architecture-era/<slug>]
led_to: [architecture-era/<slug>]

# ── Teaching layer (all optional; author every one to the anyone-can-understand bar) ──
analogy:
  title: "<short metaphor name>"
  body: "<one vivid real-world metaphor that makes the mechanism click>"
narrative:
  motivation: "<why it was developed — the problem with what came before>"
  approach: "<the core idea / bet>"
  solution: "<how they actually built it>"
  outcome: "<what it unlocked / how the field changed>"
key_equation:
  latex: "<LaTeX, no surrounding $>"
  plain: "<what the equation says, in plain English>"
  terms:
    - { symbol: "<x>", meaning: "<what it is>" }
strengths: ["<strength>"]
weaknesses: ["<weakness>"]
diagram: |
  flowchart TD
    A[Input] --> B[...]
diagram_caption: "<one-line caption>"
underlying_model_note: "<optional: how a catalog model's whole architecture, shown as a second figure, relates to the idea above; sourced>"
# ── Schema 2.5 teaching parts (optional; write them together with the How It Works and Worked Example sections) ──
visual:                          # computed figure for the Worked Example; its inputs are the example's numbers
  kind: distribution             # or one of the kinds in schema/architecture-era.schema.json $defs:
                                 # recurrent_memory, convolution, noise_schedule, residual_gradient,
                                 # discriminator, rotary, selective_scan, loss_formula,
                                 # group_advantages, perceptron_training, backprop, gaussian_code
  tokens: ["<item>", "<item>"]   # what is scored: keys of an attention example, experts of a router
  logits: [<score>, <score>]
  temperatures: [1]              # or truncate: { top_k: <k> }
  panel_titles: ["<what this panel shows>"]
  caption: "<what is drawn, and that the scores are an example>"
comparison:
  before: "<the earlier approach>"
  after: "<this architecture>"
  rows:
    - { aspect: "<what is compared>", before: "<earlier>", after: "<this>", source: "<url in sources>" }
key_numbers:
  columns: ["<first column>", "<column>"]
  rows:
    - { cells: ["<cell>", "<cell>"], source: "<url in sources>" }
misunderstandings:
  - { misunderstanding: "<what people often think>", truth: "<what is actually true; Markdown>", source: "<url in sources>" }
notable_models:
  - { model: model/<slug>, year: <YYYY>, note: "<what it proved>" }   # the catalog has the record: its title is the name shown
  - { name: "<Model>", year: <YYYY>, note: "<what it proved>" }       # no record: the name as written
resources:
  - { kind: blog, title: "<title>", url: "<url>" }   # kind: paper | blog | video | course | tutorial
learn_first:
  - { label: "<prerequisite>", id: architecture-era/<slug>, tag: Architecture }   # a record: the consumer links to its page
  - { label: "<prerequisite with no record yet>", tag: "<Math | Concept>" }       # never a site route
tags: [<tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.95 }
---

## What Changed
<Optional "go deeper" prose — the fuller narrative beyond the structured fields above.>

## Key Ideas
- <idea>
- <idea>

## How It Works
<Optional (schema 2.5): numbered steps tied to the blocks of the main figure: what goes in, what each
block does, what comes out; one to three sentences per step, every term defined at first use.>

## Worked Example
<Optional (schema 2.5): the core computation on tiny numbers, every step numbered with the numbers
substituted, ending with "What this means". Its inputs are the `visual` scores.>
