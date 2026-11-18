---
id: task/<slug>
type: task
slug: <slug>
schema_version: 1.0
title: "<Task Name>"
group: task-group/<slug>
parent: task/<slug>             # optional: only for a narrower task under a broader one
order: 1                        # display position inside the group
summary: "<one line: the job a model of this task does>"
hf_pipeline_tags: [<hugging-face-pipeline-tag>]   # [] when Hugging Face has no tag
inputs: [text]                  # see the modality list in schema/task.schema.json
outputs: [text]
benchmarks: [benchmark/<slug>]  # optional: benchmarks that measure quality here
tags: [<tag>]
updated: <YYYY-MM-DD>
sources:
  - { url: "<url>", title: "<title>", trust: 0.9 }
---

## What It Is
<The job in plain language: what goes in, what comes out.>

## When To Use
<Concrete situations that call for this task, and the neighbouring task to use instead when they do not fit.>

## How To Choose
<What separates a good model from a poor one for this task: the properties to compare.>
