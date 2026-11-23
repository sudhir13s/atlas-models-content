---
id: task/image-generation
type: task
slug: image-generation
schema_version: 3.1
title: Image Generation
group: task-group/generation
order: 1
summary: Create a new image from a text description, or from nothing but random noise.
hf_pipeline_tags:
- text-to-image
- unconditional-image-generation
inputs:
- text
outputs:
- image
tags:
- generative
- image
- diffusion
updated: '2026-11-18'
sources:
- url: https://huggingface.co/tasks/text-to-image
  title: Hugging Face — Text-to-Image task
  trust: 0.9
---

## What It Is

An image generation model **produces a picture that did not exist before**.

- **Text-to-image:** a written prompt describes the subject, style and composition of the result.
- **Unconditional generation:** the model samples images that resemble its training data, with no prompt.
- **Iterative sampling:** most models refine noise into an image over several steps, so speed depends on step count.

## When To Use

- **Use it** when you start from an idea in words and need a new picture.
- **Use image editing and upscaling instead** when you already have an image to change, extend or sharpen.
- **Use video generation instead** when the result must move, and 3D generation when it must be viewed from any angle.

## How To Choose

- **Prompt adherence:** how faithfully the picture follows long or detailed descriptions.
- **Resolution and aspect ratios:** the output sizes the model handles without distortion.
- **Memory and speed:** the graphics memory needed and the number of sampling steps per image.
- **Licence:** open weights do not always allow commercial use of the outputs.
