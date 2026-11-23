---
id: milestone/<year>-<slug>
type: milestone
slug: <year>-<slug>
schema_version: 3.1              # the schema's current version; the schema refuses any other
title: "<The event, as a short phrase>"
year: <year>
order: 1                        # position in the timeline
architectures: [architecture-era/<slug>]   # eras the event names; [] when none
models: [model/<slug>]                     # models the event names; [] when none
tags: [<tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
---

## Summary
<What happened, in a few lines. Optional.>
