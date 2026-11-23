---
id: quantization-topic/<slug>
type: quantization-topic
slug: <slug>
schema_version: 3.1              # the schema's current version; the schema refuses any other
title: "<Page title>"
order: 1                        # position in the guide's reading order
summary: "<One plain sentence: what the page explains.>"
visuals:                        # optional; each opens the body section named in `section`
  - kind: bit_layout            # bit_layout | affine_example | model_memory | name_decoder
    section: "<Body heading>"
    formats:
      - name: "<format>"
        fields: [{ role: sign, bits: 1 }, { role: exponent, bits: 8 }, { role: mantissa, bits: 7 }]
    caption: "<What the figure shows; name example values as examples.>"
  - kind: affine_example
    section: "<Body heading>"
    bits: 4
    weights: [<w1>, <w2>, <w3>, <w4>]   # an example, not from a trained model; say so in the caption
    caption: "<caption>"
  - kind: model_memory
    section: "<Body heading>"
    model: model/<slug>                 # a model record with total_parameters
    precisions: [{ label: "<label>", bits: 16 }, { label: "<label>", bits: 4 }]
    caption: "<caption>"
  - kind: name_decoder
    section: "<Body heading>"
    examples:
      - name: "<name>"
        parts: [{ token: "<part>", explains: "<what it means, from a source>" }]
    caption: "<caption>"
tables:                         # optional; every row names one entry of sources
  - section: "<Body heading>"
    title: "<Table title>"
    columns: ["<column>", "<column>"]
    rows:
      - { cells: ["<cell>", "<cell>"], source: "<url of one entry of sources>" }
tags: [quantization]
updated: <YYYY-MM-DD>
related: []                     # IDs of the pages to read next
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
---

## What It Is
<A bold one-line lead, then labelled bullets: what the page is about, every term defined at first use.>

## <Topic heading named by a figure or table>
<The text under the figure or table: a bold lead, then bullets. Every number names its source; a computed number shows its formula.>

## What To Watch
<Optional: the traps a reader meets, each from a source.>
