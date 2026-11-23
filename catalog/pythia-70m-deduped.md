---
id: model/pythia-70m-deduped
type: model
slug: pythia-70m-deduped
schema_version: 3.0
title: pythia-70m-deduped
organization: organization/eleutherai
release_date: '2023-02-13'
license: license/apache-2-0
architecture_id: architecture-era/transformer
model_category: language
model_kind: transformer-language
tasks:
- task/text-generation
parameters_active_b: 0.1
total_parameters: 95592496
context_window_tokens: 2048
summary: Pythia-70M-deduped is the smallest model of the Pythia Scaling Suite from EleutherAI, an English-language Transformer language model with about 70 million parameters. It was trained on a version of the Pile, a large text dataset, from which duplicate documents were removed. The card says the suite was developed to support scientific research on large language models, especially interpretability research.
intended_use: The card names research on the behavior, functionality and limitations of large language models as the primary use, and says the suite gives a controlled setting for experiments. It says Pythia is not intended for deployment or human-facing interaction, is English-only, and will not follow prompts the way a chat product does because it was not fine-tuned for that.
what_sets_it_apart: The suite has two sets of eight model sizes, one trained on the Pile and one on the deduplicated Pile, and all sizes are trained on the same data in the same order. The card says 154 intermediate checkpoints per model are hosted as branches, so researchers can study a model at different stages of training.
fingerprint:
  attention_type: MHA
  num_layers: 6
  attention_heads: 8
  kv_heads: 8
  head_size: 64
  hidden_size: 512
  intermediate_size: 2048
  vocabulary_size: 50304
  activation_function: gelu
  normalization: LayerNorm
  norm_placement: pre
  stack: decoder
  parallel_sublayers: separate-norms
  positional_encoding: RoPE
  rope_theta: 10000.0
filters:
  size_category: tiny
  is_quantized: false
  is_moe: false
  is_gated: false
  pipeline_tag: text-generation
  library: transformers
hub_signals:
  downloads_30d: 506459
  likes: 39
  last_modified: '2023-07-09'
  observed_at: '2026-11-20'
  source: https://huggingface.co/EleutherAI/pythia-70m-deduped
aliases:
- EleutherAI/pythia-70m-deduped
lineage:
  parents: []
  children: []
quotes:
  summary: The Pythia model suite was designed to promote scientific research on large language models, especially interpretability research.
  intended_use: The primary intended use of Pythia is research on the behavior, functionality, and limitations of large language models.
  what_sets_it_apart: All 8 model sizes are trained on the exact same data, in the exact same order.
tags:
- gpt-neox
updated: '2026-11-23'
sources:
- url: https://huggingface.co/EleutherAI/pythia-70m-deduped
  title: EleutherAI/pythia-70m-deduped — model page (Hugging Face)
  trust: 0.9
- url: https://huggingface.co/EleutherAI/pythia-70m-deduped/blob/main/config.json
  title: EleutherAI/pythia-70m-deduped — config.json
  trust: 0.9
- url: https://arxiv.org/abs/2304.01373
  title: arXiv:2304.01373
  trust: 0.95
- url: https://arxiv.org/abs/2101.00027
  title: arXiv:2101.00027
  trust: 0.95
- url: https://arxiv.org/abs/2201.07311
  title: arXiv:2201.07311
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt_neox/modeling_gpt_neox.py
  title: 'transformers/models/gpt_neox/modeling_gpt_neox.py: reference implementation (Hugging Face Transformers)'
  trust: 0.9
---


