---
id: architecture-era/gpt
type: architecture-era
slug: gpt
schema_version: 1.0
title: "GPT (Decoder-Only Autoregressive LMs)"
name: "GPT"
era: scaling
era_range: "2018–2020"
year: 2020
family: llm
core_innovation: "Decoder-only autoregressive pretraining that, scaled up, yields in-context (few-shot) learning without task-specific fine-tuning."
description: "The decoder-only generative line; at 175 billion parameters, GPT-3 performed tasks from few-shot examples in the prompt without gradient updates."
key_features: ["Decoder-only transformer", "Causal (left-to-right) language modeling", "Generative pretraining on unlabeled text", "Generation by sampling one token at a time"]
key_models: ["GPT (2018)", "GPT-2 (2019)", "GPT-3 175B (2020)"]
paper_title: "Language Models are Few-Shot Learners (GPT-3)"
paper_url: "https://arxiv.org/abs/2005.14165"
influenced_by: [architecture-era/transformer]
led_to: [architecture-era/llama-recipe, architecture-era/scaling-laws, architecture-era/rlhf]

eli5: "GPT is autocomplete trained on a large amount of text: the model learns to guess the next word, over and over. With GPT-3, the authors showed that a large enough model can do a new task from a plain-language description and a few examples, with no retraining."

analogy:
  title: "Predictive autocomplete"
  body: "Your phone keyboard predicts the next word from the last few you typed. GPT is the same idea, always guessing the next token, but trained on far more text and with far more parameters. Given 'English: hello / French: bonjour / English: goodbye / French: ___', a model of this kind continues the pattern; GPT-3 was tested this way, with tasks and examples given only as text."

narrative:
  motivation: "Labeled data for specific language tasks is scarce while unlabeled text is abundant. The question was whether one simple objective on unlabeled text could produce a model that both generates text and handles many tasks, without a bespoke architecture per task."
  approach: "Take the transformer's decoder and train it to predict the next token given all the previous ones (causal, left-to-right language modeling). A triangular attention mask ensures each position sees only the past, so the same network trains by prediction and generates by sampling one token at a time."
  solution: "GPT (2018) showed generative pretraining followed by discriminative fine-tuning improved the state of the art on 9 of 12 tasks studied. GPT-2 (2019), at 1.5 billion parameters, performed tasks zero-shot. GPT-3 (2020), at 175 billion parameters, was evaluated few-shot: tasks and demonstrations are given purely as text in the prompt, with no gradient updates."
  outcome: "The GPT-3 authors report that scaling up language models greatly improves task-agnostic few-shot performance, sometimes reaching competitiveness with earlier fine-tuned systems. InstructGPT (2022) later fine-tuned GPT-3 with human feedback to follow instructions."

build_steps:
  - { title: "1. Tokenize the text", detail: "Break text into byte-pair-encoding (BPE) subword tokens (byte-level BPE in GPT-2) and map each to a learned embedding vector." }
  - { title: "2. Add positional information", detail: "Because attention is order-blind, add a positional signal (GPT-1 used learned position embeddings) so the model knows token order." }
  - { title: "3. Apply a causal mask", detail: "In self-attention, block every position from seeing future tokens with a triangular mask. Each token may attend only to itself and the past — the definition of autoregressive." }
  - { title: "4. Stack decoder blocks", detail: "Run the sequence through N transformer-decoder blocks (masked self-attention + feed-forward, with residuals and normalization): 12 in GPT-1, 48 in the largest GPT-2." }
  - { title: "5. Predict the next token", detail: "At each position, project the final vector over the vocabulary and train with cross-entropy against the actual next token — the single, self-supervised training signal." }
  - { title: "6. Generate by sampling", detail: "At inference, feed a prompt, sample the next token from the output distribution (temperature, top-p), append it, and repeat — the model writes one token at a time." }

key_equation:
  latex: "p(x_1,\\dots,x_T)=\\prod_{t=1}^{T} p\\!\\left(x_t \\mid x_{<t}\\right)"
  plain: "The probability of a whole sequence is the product of the probability of each token given everything before it. Training maximizes this — equivalently, minimizes the negative log-likelihood — so the model gets good at predicting the next token, one step at a time, using only the past."
  terms:
    - { symbol: "x_1,\\dots,x_T", meaning: "the sequence of tokens (a document), left to right" }
    - { symbol: "x_t", meaning: "the token at position t — the one being predicted" }
    - { symbol: "x_{<t}", meaning: "all tokens before position t — the only context the causal model may use" }
    - { symbol: "p(x_t \\mid x_{<t})", meaning: "the model's predicted probability of the next token given the past" }
    - { symbol: "\\prod", meaning: "the product over all positions — the chain rule of probability factorizing the sequence" }

strengths:
  - "One self-supervised objective (next-token prediction) that needs no labeled data."
  - "Few-shot learning at scale: GPT-3 handles tasks from demonstrations in the prompt, with no gradient updates."
  - "Natively generative: the same network that trains by prediction produces text by sampling."
  - "Tasks can be specified as natural-language text instead of task-specific fine-tuning datasets."

weaknesses:
  - "Only left context: causal masking means a token can't use words to its right, unlike BERT's bidirectional encoding."
  - "Next-token training does not by itself align a model with user intent; the InstructGPT authors report untruthful, toxic or unhelpful outputs from large language models."
  - "Generation is sequential, one token at a time."
  - "The GPT-3 authors report datasets where few-shot learning still struggles."

diagram: |
  flowchart TD
    P["Prompt tokens"] --> EMB["Token + position embeddings"]
    EMB --> DEC["Masked (causal) decoder blocks x N"]
    DEC --> LOGITS["Next-token distribution over vocab"]
    LOGITS --> SAMPLE["Sample one token"]
    SAMPLE --> APPEND["Append to sequence"]
    APPEND -->|feed back| EMB
    SAMPLE --> OUT["Generated text"]
diagram_caption: "GPT reads the context with causally-masked decoder blocks, predicts the next token, samples it, appends it, and repeats — generating one token at a time."

notable_models:
  - { name: "GPT", year: 2018, note: "The original: generative pretraining of a 12-layer decoder, then discriminative fine-tuning; state of the art on 9 of 12 tasks studied." }
  - { name: "GPT-2", year: 2019, note: "1.5B parameters trained on WebText; reported zero-shot results on many language modeling datasets." }
  - { name: "GPT-3", year: 2020, note: "175B parameters, evaluated few-shot: tasks and demonstrations given as text, with no gradient updates or fine-tuning." }
  - { name: "InstructGPT", year: 2022, note: "GPT-3 fine-tuned on demonstrations and then with reinforcement learning from human feedback; labelers preferred the 1.3B model's outputs to the 175B GPT-3's." }
  - { name: "LLaMA", year: 2023, note: "7B–65B models trained only on publicly available data; LLaMA-13B outperforms GPT-3 (175B) on most benchmarks, per the authors." }

resources:
  - { kind: paper, title: "Language Models are Few-Shot Learners (Brown et al., 2020)", url: "https://arxiv.org/abs/2005.14165" }
  - { kind: video, title: "Let's build GPT from scratch (Andrej Karpathy)", url: "https://www.youtube.com/watch?v=kCc8FmEb1nY" }
  - { kind: blog, title: "The Illustrated GPT-2 (Jay Alammar)", url: "https://jalammar.github.io/illustrated-gpt2/" }
  - { kind: tutorial, title: "nanoGPT — minimal GPT training code (Andrej Karpathy)", url: "https://github.com/karpathy/nanoGPT" }

learn_first:
  - { label: "The Transformer (decoder half)", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "BERT (the encoder-only complement)", href: "/models/architecture/bert", tag: "Architecture" }
  - { label: "Scaling laws — how big to train", href: "/models/architecture/scaling-laws", tag: "Concept" }
  - { label: "Softmax, sampling & temperature", href: "/learn/concepts/softmax", tag: "Math" }

tags: [gpt, decoder, autoregressive, in-context-learning, llm]
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (Brown et al., 2020)", trust: 0.95 }
  - { url: "https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf", title: "Language Models are Unsupervised Multitask Learners (GPT-2, Radford et al., 2019)", trust: 0.9 }
  - { url: "https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf", title: "Improving Language Understanding by Generative Pre-Training (GPT, Radford et al., 2018)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2203.02155", title: "Training language models to follow instructions with human feedback (Ouyang et al., 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2302.13971", title: "LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)", trust: 0.95 }
  - { url: "https://jalammar.github.io/illustrated-gpt2/", title: "The Illustrated GPT-2 (Jay Alammar)", trust: 0.9 }
---

## What Changed
The GPT line uses the transformer **decoder**, trained on one objective: predict the next token given
all previous ones.

- **GPT (2018)**: generative pretraining, then fine-tuning on each task.
- **GPT-2 (2019)**: 1.5B parameters, evaluated on tasks zero-shot.
- **GPT-3 (2020)**: 175B parameters, evaluated **few-shot**: a task and a few examples are given *in
  the prompt*, and the model performs it with no gradient updates.

## Key Ideas
- **Decoder-only, causal**: a triangular attention mask lets each token see only the past, so the
  same network trains by prediction and generates by sampling.
- **Scale**: the GPT-3 authors report that scaling up language models greatly improves task-agnostic,
  few-shot performance.
- **In-context learning**: few-shot examples in the prompt steer a model whose weights stay fixed.
- **Prompting as interface**: the task is specified as text instead of a task-specific fine-tuning
  dataset.
