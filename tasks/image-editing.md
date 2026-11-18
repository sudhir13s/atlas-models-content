---
id: task/image-editing
type: task
slug: image-editing
schema_version: 1.0
title: "Image Editing and Upscaling"
group: task-group/generation
order: 2
summary: "Change, restyle, repair or enlarge an existing image, optionally guided by a written instruction."
hf_pipeline_tags: [image-to-image, image-text-to-image]
inputs: [image, text]
outputs: [image]
tags: [generative, image, editing]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/image-to-image", title: "Hugging Face — Image-to-Image task", trust: 0.9 }
---

## What It Is
An image editing model takes a picture in and **returns a modified version of that picture**.

- **Inpainting:** fill or replace a masked region so it blends with the rest of the image.
- **Restyling:** keep the layout and subject but change the look, such as photo to sketch.
- **Super-resolution:** enlarge an image and restore detail that the small version lacks.
- **Instruction-based edits:** apply a change described in plain words, with no mask drawn by hand.

## When To Use
- **Use it** when a source image exists and most of it should survive the change.
- **Use image generation instead** when there is no source image and the picture starts from a prompt.
- **Use image segmentation first** when you need a precise mask for the region to edit.

## How To Choose
- **Edit type supported:** inpainting, restyling, upscaling and instruction edits are often separate models.
- **Fidelity to the source:** how well untouched areas, faces and identities are preserved.
- **Control inputs:** whether the model accepts masks, reference images, edges or depth maps.
- **Output size and memory:** the largest result the model produces cleanly, and the memory that takes.
