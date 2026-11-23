---
id: license/<slug>
type: license
slug: <slug>
schema_version: 3.1              # the schema's current version; the schema refuses any other
title: "<License Name>"
name: "<License Name>"
spdx_id: <SPDX-ID>
category: permissive            # permissive | copyleft | restricted | custom
url: "<license url>"
permissions:
  commercial_use: true
  modification: true
  distribution: true
  fine_tuning: true
  output_training: true
  patent_grant: false
  private_use: true
conditions: ["<condition>"]
limitations: ["<limitation>"]
summary: "<one-line plain-English summary>"
tags: [<tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.95 }
---

## Plain-English
<What you can and cannot do, in plain language.>
