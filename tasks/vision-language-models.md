---
id: task/vision-language-models
type: task
slug: vision-language-models
schema_version: 1.0
title: "Vision-Language Models"
group: task-group/vision
order: 1
summary: "Read an image together with a text prompt and reply in text: answer questions, caption, or read charts."
hf_pipeline_tags: [image-text-to-text, visual-question-answering, image-to-text]
inputs: [image, text]
outputs: [text]
tags: [multimodal, vision, generative]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/image-text-to-text", title: "Hugging Face — Image-Text-to-Text task", trust: 0.9 }
---

## What It Is
A vision-language model (VLM) takes one or more images plus a prompt and **writes a text reply**.

- **Visual question answering:** answer a specific question about what the image shows.
- **Captioning:** describe an image without being asked a question.
- **Chart and screenshot reading:** pull values and relationships out of diagrams, plots and interfaces.

## When To Use
- **Use it** when the request about an image changes from call to call or needs a free-form answer.
- **Use image classification or object detection instead** for a fixed set of labels or exact box positions; they are faster and cheaper.
- **Use optical character recognition (OCR) instead** when the job is to transcribe a full page faithfully.
- **Use video understanding instead** when the answer depends on motion across frames.

## How To Choose
- **Image resolution:** higher input resolution is needed for small text and dense charts.
- **Images per prompt:** some models accept only one image, others several or interleaved images and text.
- **Size against hardware:** the image encoder adds memory on top of the language model.
- **Context window:** each image uses part of it, which limits how many fit alongside the prompt.
