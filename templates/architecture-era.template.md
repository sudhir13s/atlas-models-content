---
id: architecture-era/<slug>
type: architecture-era
slug: <slug>
schema_version: 1.0
title: "<Human Readable Name>"
name: "<Name>"
era: scaling                     # early | foundation | scaling | post-transformer
era_range: "<start–end>"
year: <YYYY>
family: llm                      # classical-nn | classical-dl | llm | vision | generation | multimodal | other
core_innovation: "<the one thing it introduced>"
description: "<one-line description>"
key_features: ["<feature>", "<feature>"]
key_models: ["<Model (year)>"]
paper_title: "<Paper Title>"
paper_url: "<arxiv url>"
influenced_by: [architecture-era/<slug>]
led_to: [architecture-era/<slug>]

# ── Teaching layer (all optional; author every one to the anyone-can-understand bar) ──
eli5: "<2-3 lines, zero jargon: what is this, really?>"
analogy:
  title: "<short metaphor name>"
  body: "<one vivid real-world metaphor that makes the mechanism click>"
narrative:
  motivation: "<why it was developed — the problem with what came before>"
  approach: "<the core idea / bet>"
  solution: "<how they actually built it>"
  outcome: "<what it unlocked / how the field changed>"
build_steps:
  - { title: "<plain-language step name>", detail: "<what happens in this step and why>" }
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
notable_models:
  - { name: "<Model>", year: <YYYY>, note: "<what it proved>" }
resources:
  - { kind: blog, title: "<title>", url: "<url>" }   # kind: paper | blog | video | course | tutorial
learn_first:
  - { label: "<prereq concept>", href: "<internal or external href>", tag: "<Math | Concept | Architecture>" }
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
