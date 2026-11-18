---
id: task/image-and-multimodal-embeddings
type: task
slug: image-and-multimodal-embeddings
schema_version: 1.0
title: "Image and Multimodal Embeddings"
group: task-group/retrieval
order: 3
summary: "Turn images, page images or mixed image-and-text inputs into vectors that can be searched and compared."
hf_pipeline_tags: [image-feature-extraction, visual-document-retrieval]
inputs: [image, document, text]
outputs: [embedding]
tags: [embeddings, multimodal, retrieval]
updated: 2026-11-18
sources:
  - { url: "https://huggingface.co/tasks/image-feature-extraction", title: "Hugging Face — Image Feature Extraction task", trust: 0.9 }
---

## What It Is
These models map visual content into **vectors**, often in a space shared with text.

- **Image backbones** produce general-purpose features that other vision models build on.
- **Shared image-text models** place a picture and its description close together, so text can search images.
- **Visual document retrieval** embeds a whole page as an image, keeping layout, tables and figures without text extraction.

## When To Use
- **Use it** for image search, finding near-duplicate pictures, or retrieving pages from scanned documents.
- **Use embedding models instead** when the collection is clean text; they are smaller and cheaper.
- **Use optical character recognition (OCR) first** when you need the page text itself, not only the matching page.
- **Use a vision-language model instead** when the goal is a written answer about an image.

## How To Choose
- **Supported inputs:** image only, image and text in one space, or full document pages.
- **Vector size and count:** one vector per item is cheap; many vectors per page are more precise but heavier to store.
- **Image resolution:** small text and fine detail are lost if the model downsizes the input too far.
- **Languages:** for text queries, check which languages the text side was trained on.
