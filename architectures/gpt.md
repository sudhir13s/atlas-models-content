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
description: "The decoder-only generative line whose scaling produced emergent few-shot, in-context learning."
key_features: ["Decoder-only transformer", "Causal (left-to-right) language modeling", "Massive scale", "In-context / few-shot learning", "Task-agnostic prompting"]
key_models: ["GPT (2018)", "GPT-2 (2019)", "GPT-3 175B (2020)"]
paper_title: "Language Models are Few-Shot Learners (GPT-3)"
paper_url: "https://arxiv.org/abs/2005.14165"
influenced_by: [architecture-era/transformer]
led_to: [architecture-era/llama-recipe, architecture-era/scaling-laws, architecture-era/rlhf]

eli5: "GPT is autocomplete taken to the extreme. Train a model to guess the next word, over and over, on a huge slice of the internet, and make it big enough — and it stops being just autocomplete. You can now describe a task in plain language, and it does it, no retraining needed."

analogy:
  title: "Predictive autocomplete that got a brain"
  body: "Your phone keyboard predicts the next word from the last few you typed. GPT is the same idea — always guessing the next token — but trained on far more text and made vastly larger. Scale changes it qualitatively: a tiny autocomplete finishes 'good mor___'; a giant one, given 'English: hello / French: bonjour / English: goodbye / French: ___', continues the pattern with 'au revoir'. Same next-word game, but big enough that following the pattern in the prompt looks like understanding the task."

narrative:
  motivation: "BERT had shown the transformer encoder was great for understanding, but understanding isn't generation. The open question was whether one simple, scalable objective could produce a model that both writes fluent text and handles many tasks — without building a bespoke system per task."
  approach: "Take the transformer's decoder and train it on the most basic objective imaginable: predict the next token given all the previous ones (causal, left-to-right language modeling). A triangular attention mask ensures each position sees only the past, so the exact same network trains by prediction and generates by sampling one token at a time."
  solution: "The surprise was what scale did to this objective. GPT (2018) showed a pretrained decoder fine-tunes well across tasks. GPT-2 (2019) showed it does many tasks zero-shot. GPT-3 (2020), at 175 billion parameters, showed in-context learning: describe a task and give a few examples inside the prompt, and the frozen model performs it with no weight updates at all."
  outcome: "This collapsed 'pretrain, then fine-tune a model per task' into 'pretrain once, then prompt.' Next-token prediction turned out to be a general-purpose training signal, and natural language became the interface to AI. GPT descends from the transformer and directly motivated the scaling-laws work that governs how big to train and the RLHF step that turned raw predictors into helpful assistants."

build_steps:
  - { title: "1. Tokenize the text", detail: "Break text into byte-pair-encoding (BPE) subword tokens and map each to a learned embedding vector — the model's input alphabet." }
  - { title: "2. Add positional information", detail: "Because attention is order-blind, add a positional signal (learned in early GPTs) so the model knows token order." }
  - { title: "3. Apply a causal mask", detail: "In self-attention, block every position from seeing future tokens with a triangular mask. Each token may attend only to itself and the past — the definition of autoregressive." }
  - { title: "4. Stack decoder blocks", detail: "Run the sequence through N transformer-decoder blocks (masked self-attention + feed-forward, with residuals and normalization). Depth builds the model's capability." }
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
  - "One simple, self-supervised objective (next-token prediction) scales to trillions of tokens with no labeling."
  - "In-context / few-shot learning — a frozen model handles new tasks from prompt examples alone, no retraining."
  - "Natively generative: the same network that trains by prediction produces fluent text by sampling."
  - "One model, countless tasks — the interface moves from labeled datasets to natural-language prompts."

weaknesses:
  - "Only left context — causal masking means a token can't use words to its right, unlike BERT's bidirectional encoding."
  - "Raw next-token training optimizes plausibility, not truth or helpfulness — hence hallucination and the need for RLHF."
  - "Generation is inherently sequential (one token at a time), making long outputs slow at inference."
  - "Capability rides on scale — the strongest behaviors need enormous parameter and compute budgets."

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
  - { name: "GPT", year: 2018, note: "The original — proved generative pretraining of a decoder transfers across tasks via light fine-tuning." }
  - { name: "GPT-2", year: 2019, note: "Scaled to 1.5B params and showed surprisingly strong zero-shot task performance and coherent long-form generation." }
  - { name: "GPT-3", year: 2020, note: "At 175B params, demonstrated in-context few-shot learning — new tasks from prompt examples with no weight updates." }
  - { name: "InstructGPT", year: 2022, note: "Added RLHF so the model follows instructions and is helpful — the bridge from raw predictor to usable assistant." }
  - { name: "LLaMA", year: 2023, note: "Showed an open, compute-optimal decoder-only recipe could match much larger closed models at far fewer parameters." }

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
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (Brown et al., 2020)", trust: 0.95 }
  - { url: "https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf", title: "Language Models are Unsupervised Multitask Learners (GPT-2, Radford et al., 2019)", trust: 0.9 }
  - { url: "https://jalammar.github.io/illustrated-gpt2/", title: "The Illustrated GPT-2 (Jay Alammar)", trust: 0.9 }
---

## What Changed
Where BERT used the transformer encoder for understanding, the GPT line used the **decoder** for
generation, trained on a single, brutally simple objective: predict the next token given all previous
ones. The surprise was what scale did to this objective.

GPT (2018) showed a pretrained decoder could be fine-tuned across tasks; GPT-2 (2019) showed it could
do many tasks zero-shot; and GPT-3 (2020), at 175B parameters, showed **in-context learning** — you
describe a task and give a few examples *in the prompt*, and the frozen model performs it with no
weight updates at all.

This collapsed the "pretrain then fine-tune per task" workflow into "pretrain once, then prompt."
Next-token prediction turned out to be a general-purpose training signal, and the practical interface
to AI became natural language. GPT descends from the transformer and directly motivated the
scaling-laws work that governs how big to train, the open LLaMA recipe, and the RLHF alignment step
that made these models usable as assistants.

## Key Ideas
- **Decoder-only, causal** — a triangular attention mask lets each token see only the past, so the
  same network trains by prediction and generates by sampling.
- **Scale as a lever** — capability rose smoothly and sometimes discontinuously with parameters, data,
  and compute; bigger models did qualitatively new things.
- **In-context learning** — few-shot examples in the prompt steer a frozen model, trading fine-tuning
  for prompting and making one model serve countless tasks.
- **Prompting as interface** — the task specification moves from labelled datasets to natural-language
  instructions, reshaping how people build on top of models.
