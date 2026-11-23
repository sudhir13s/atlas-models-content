---
id: model/minimax-h3
type: model
slug: minimax-h3
schema_version: 1.1
title: MiniMax-H3
organization: organization/minimaxai
release_date: '2026-07-28'
release_year: 2026
release_month: 7
license_note: minimax-h3-community-license-agreement
model_category: generation
tasks:
- task/video-generation
total_parameters: 33122992896
summary: MiniMax H3 is a video generation system from MiniMax that also produces sound. According to the model card it accepts text, images, video and audio as input and generates video with native stereo audio, in clips of 4 to 15 seconds and at up to 2K resolution. It is described as a general-purpose, omni-modal generative system made of three modules.
what_sets_it_apart: 'The card says the system has three modules: H3-Context-IR, which interprets the inputs and turns them into a form the generator understands, H3-Base, which produces video and audio at 768p, and H3-Regenerate-2K, which re-generates the result at 2K. The authors attribute its handling of complex multimodal instructions to a task-generalization-oriented system design.'
running_it: The card says H3-Context-IR relies on several hosted models and services and is not part of the open-source release. It offers an API that reproduces the official workflow and says developers can follow its Prompting Guidance to build their own preprocessing, and it strongly recommends including this step.
filters:
  size_category: large
  is_gated: false
  pipeline_tag: image-text-to-video
  library: minimax-h3
hub_signals:
  downloads_30d: 5752082
  likes: 7280
  last_modified: '2026-11-20'
  observed_at: '2026-11-21'
  source: https://huggingface.co/MiniMaxAI/MiniMax-H3
aliases:
- MiniMaxAI/MiniMax-H3
lineage:
  parents: []
  children: []
quotes:
  summary: MiniMax H3 is a general-purpose, omni-modal generative system.
  what_sets_it_apart: Thanks to its task-generalization-oriented system design
  running_it: H3-Context-IR is critical to the quality of the final output, so we strongly recommend incorporating it into your generation pipeline
tags:
- image-text-to-video
updated: '2026-11-23'
sources:
- url: https://huggingface.co/MiniMaxAI/MiniMax-H3
  title: MiniMaxAI/MiniMax-H3 — model page (Hugging Face)
  trust: 0.9
---

## Summary

MiniMax H3 is a video generation system from MiniMax that also produces sound. According to the model card it accepts text, images, video and audio as input and generates video with native stereo audio, in clips of 4 to 15 seconds and at up to 2K resolution. It is described as a general-purpose, omni-modal generative system made of three modules.

## What Sets It Apart

The card says the system has three modules: H3-Context-IR, which interprets the inputs and turns them into a form the generator understands, H3-Base, which produces video and audio at 768p, and H3-Regenerate-2K, which re-generates the result at 2K. The authors attribute its handling of complex multimodal instructions to a task-generalization-oriented system design.

## Running It

The card says H3-Context-IR relies on several hosted models and services and is not part of the open-source release. It offers an API that reproduces the official workflow and says developers can follow its Prompting Guidance to build their own preprocessing, and it strongly recommends including this step.
