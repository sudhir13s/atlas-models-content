---
id: architecture-era/bert
type: architecture-era
slug: bert
schema_version: 1.0
title: "BERT (Bidirectional Pretraining)"
name: "BERT"
era: scaling
era_range: "2018–2020"
year: 2018
family: llm
core_innovation: "Encoder-only bidirectional pretraining via masked language modeling, producing reusable contextual representations for fine-tuning."
description: "The encoder-only transformer whose masked-LM pretraining set the pretrain-then-fine-tune paradigm for NLP."
key_features: ["Encoder-only transformer", "Masked language modeling", "Next-sentence prediction", "Bidirectional context", "Fine-tune per downstream task"]
key_models: ["BERT-base/large (2018)", "RoBERTa (2019)", "DeBERTa (2020)"]
paper_title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding"
paper_url: "https://arxiv.org/abs/1810.04805"
influenced_by: [architecture-era/transformer]
led_to: []
tags: [bert, encoder, masked-lm, pretraining, llm]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1810.04805", title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)", trust: 0.95 }
---

## What Changed
The transformer gave a powerful architecture, but early language models still read text left-to-right,
so a word's representation could only depend on what came before it. BERT keeps just the transformer's
**encoder** and trains it with **masked language modeling**: randomly hide ~15% of tokens and ask the
model to reconstruct them from context on *both* sides. This forces genuinely bidirectional
representations — each token is encoded in light of its full surroundings — which is exactly what
understanding tasks (classification, entailment, question answering) need. Just as important was the
recipe it cemented: pretrain once on a huge unlabeled corpus, then **fine-tune** a small task-specific
head on each downstream dataset. BERT swept the NLP benchmarks of its day and made "download a
pretrained encoder and fine-tune" the default workflow. It sits on the transformer branch; its
encoder-only, understanding-first design is the complement to the decoder-only, generation-first GPT
line.

## Key Ideas
- **Encoder-only** — no autoregressive decoder; the goal is rich representations, not left-to-right
  generation.
- **Masked language modeling** — predicting hidden tokens from two-sided context yields deeply
  bidirectional embeddings, unlike causal LMs.
- **Pretrain then fine-tune** — one expensive unsupervised pretraining amortises across many cheap
  supervised fine-tunes, transferring general language knowledge.
- **Task heads** — `[CLS]`/token representations feed lightweight heads for classification, tagging,
  and span prediction; later work (RoBERTa) showed the pretraining recipe mattered more than the
  auxiliary next-sentence objective.
