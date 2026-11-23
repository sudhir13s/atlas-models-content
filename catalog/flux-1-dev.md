---
id: model/flux-1-dev
type: model
slug: flux-1-dev
schema_version: 1.1
title: FLUX.1-dev
organization: organization/black-forest-labs
release_date: '2024-07-31'
release_year: 2024
release_month: 7
license_note: flux-1-dev-non-commercial-license
model_category: generation
tasks:
- task/image-generation
total_parameters: 11901408320
summary: 'FLUX.1 [dev] is a text-to-image model from Black Forest Labs: it generates an image from a written description. Its card calls it a 12 billion parameter rectified flow transformer, a type of image-generating neural network. The weights are open, released under the FLUX.1 [dev] Non-Commercial License, and the repository is gated, so a user must agree to the license before downloading.'
intended_use: The card says the open weights are meant to drive new scientific research and to let artists develop new workflows, and it points developers to a reference implementation as a starting point. It says generated outputs may be used for personal, scientific and commercial purposes as described in the license. Its out-of-scope list includes breaking the law, harming minors, spreading false information to harm others, exposing personal data, harassment, non-consensual nudity or illegal pornography, fully automated decisions about a person's legal rights, and large-scale disinformation.
what_sets_it_apart: The card says the model was trained using guidance distillation, which it says makes the model more efficient. Black Forest Labs also claims, in the card's own words, output quality second only to its FLUX.1 [pro] model and prompt following that matches closed source alternatives.
running_it: The card shows running it with the Diffusers library in bfloat16 precision, with a 1024 by 1024 image, a guidance scale of 3.5, 50 inference steps and a maximum prompt length of 512 tokens. It suggests CPU offloading to save graphics memory, and says the model is also available in ComfyUI for local use.
limitations: The card says the model is not intended or able to provide factual information, that as a statistical model it might amplify existing societal biases, and that it may fail to generate output that matches the prompts. It adds that prompt following is heavily influenced by the prompting style.
filters:
  size_category: medium
  is_gated: true
  pipeline_tag: text-to-image
  library: diffusers
hub_signals:
  downloads_30d: 427087
  likes: 16274
  last_modified: '2026-11-19'
  observed_at: '2026-11-21'
  source: https://huggingface.co/black-forest-labs/FLUX.1-dev
aliases:
- black-forest-labs/FLUX.1-dev
lineage:
  parents: []
  children: []
quotes:
  summary: FLUX.1 [dev] is a 12 billion parameter rectified flow transformer capable of generating images from text descriptions.
  intended_use: Open weights to drive new scientific research, and empower artists to develop innovative workflows.
  what_sets_it_apart: Trained using guidance distillation, making FLUX.1 [dev] more efficient.
  running_it: 'pipe.enable_model_cpu_offload() #save some VRAM by offloading the model to CPU.'
  limitations: This model is not intended or able to provide factual information.
tags:
- text-to-image
updated: '2026-11-23'
sources:
- url: https://huggingface.co/black-forest-labs/FLUX.1-dev
  title: black-forest-labs/FLUX.1-dev — model page (Hugging Face)
  trust: 0.9
---

## Summary

FLUX.1 [dev] is a text-to-image model from Black Forest Labs: it generates an image from a written description. Its card calls it a 12 billion parameter rectified flow transformer, a type of image-generating neural network. The weights are open, released under the FLUX.1 [dev] Non-Commercial License, and the repository is gated, so a user must agree to the license before downloading.

## Intended Use

The card says the open weights are meant to drive new scientific research and to let artists develop new workflows, and it points developers to a reference implementation as a starting point. It says generated outputs may be used for personal, scientific and commercial purposes as described in the license. Its out-of-scope list includes breaking the law, harming minors, spreading false information to harm others, exposing personal data, harassment, non-consensual nudity or illegal pornography, fully automated decisions about a person's legal rights, and large-scale disinformation.

## What Sets It Apart

The card says the model was trained using guidance distillation, which it says makes the model more efficient. Black Forest Labs also claims, in the card's own words, output quality second only to its FLUX.1 [pro] model and prompt following that matches closed source alternatives.

## Running It

The card shows running it with the Diffusers library in bfloat16 precision, with a 1024 by 1024 image, a guidance scale of 3.5, 50 inference steps and a maximum prompt length of 512 tokens. It suggests CPU offloading to save graphics memory, and says the model is also available in ComfyUI for local use.

## Limitations

The card says the model is not intended or able to provide factual information, that as a statistical model it might amplify existing societal biases, and that it may fail to generate output that matches the prompts. It adds that prompt following is heavily influenced by the prompting style.
