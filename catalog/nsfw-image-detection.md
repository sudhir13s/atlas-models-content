---
id: model/nsfw-image-detection
type: model
slug: nsfw-image-detection
schema_version: 3.0
title: nsfw_image_detection
organization: organization/falconsai
release_date: '2023-10-13'
license: license/apache-2-0
model_category: vision
model_kind: transformer-vision
tasks:
- task/image-classification
parameters_active_b: 0.09
total_parameters: 85800194
base_models:
- name: google/vit-base-patch16-224-in21k
  address: https://huggingface.co/google/vit-base-patch16-224-in21k
summary: This is a third-party fine-tune, published under the Falconsai account on Hugging Face, of the Vision Transformer (ViT) google/vit-base-patch16-224-in21k, which was pre-trained on ImageNet-21k images at 224 by 224 pixels. It is an image classifier that sorts an image into one of two classes, "normal" or "nsfw" (not safe for work). The card says it was fine-tuned on a proprietary dataset of about 80,000 images.
intended_use: The card names NSFW image classification as the primary use, for filtering explicit or inappropriate content in applications. It asks users to apply the model responsibly and ethically, following content guidelines and applicable regulations.
what_sets_it_apart: The card describes a ViT as a transformer encoder, similar to BERT, adapted to image classification. It gives the fine-tuning settings as a batch size of 16 and a learning rate of 5e-5, on a proprietary two-class dataset it does not publish.
running_it: 'The card shows two ways to use it: the Transformers image-classification pipeline, or loading the model directly with its image processor. It also includes an example of running an ONNX version with the onnxruntime library.'
limitations: The card says performance may vary on tasks other than NSFW classification, and may depend on the quality and representativeness of the fine-tuning data. It tells users to check the model's suitability for their own application and data.
fingerprint:
  attention_type: MHA
  num_layers: 12
  attention_heads: 12
  kv_heads: 12
  head_size: 64
  hidden_size: 768
  intermediate_size: 3072
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: pre
  stack: encoder
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: image-classification
  library: transformers
hub_signals:
  downloads_30d: 1565576
  likes: 1232
  last_modified: '2026-09-07'
  observed_at: '2026-11-21'
  source: https://huggingface.co/Falconsai/nsfw_image_detection
aliases:
- Falconsai/nsfw_image_detection
lineage:
  parents: []
  children: []
quotes:
  summary: The model's training data includes a proprietary dataset comprising approximately 80,000 images.
  intended_use: The primary intended use of this model is for the classification of NSFW (Not Safe for Work) images.
  what_sets_it_apart: a learning rate of 5e-5 was employed
  running_it: 'Here is how to use this model to classifiy an image based on 1 of 2 classes (normal,nsfw):'
  limitations: its performance may vary when applied to other tasks.
tags:
- vit
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Falconsai/nsfw_image_detection
  title: Falconsai/nsfw_image_detection — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/Falconsai/nsfw_image_detection/blob/main/config.json
  title: Falconsai/nsfw_image_detection — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2010.11929
  title: arXiv:2010.11929
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/vit/modeling_vit.py
  title: 'transformers/models/vit/modeling_vit.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


