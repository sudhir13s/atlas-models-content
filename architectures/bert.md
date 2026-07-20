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

eli5: "BERT learns language by playing fill-in-the-blank. Hide a few words in a sentence and make the model guess them using the words on both sides. Do that billions of times and it builds a deep sense of what words mean in context — which you can then reuse for almost any language task."

analogy:
  title: "Cloze-test study, both sides visible"
  body: "Think of the fill-in-the-blank exercises from school: 'The ___ chased the ball into the street.' To answer, you read what comes before AND after the blank — 'chased', 'ball', 'street' all hint at 'dog'. BERT studies by turning huge amounts of text into millions of these blanks and getting graded on its guesses. A left-to-right model only sees the words before the blank, like reading with a card covering the rest of the line; BERT lifts the card and reads both sides. That two-sided reading is what makes its understanding deep."

narrative:
  motivation: "The transformer was a powerful architecture, but the language models built on it still read text left-to-right, so a word's representation could only depend on what came before it. For understanding tasks — is this review positive? does sentence A entail sentence B? where's the answer in this passage? — that's a real handicap, because meaning depends on the whole context, not just the left half."
  approach: "Keep only the transformer's encoder (the part designed to build representations, not generate text) and change the training game. Instead of predicting the next word, randomly hide about 15% of the tokens and train the model to reconstruct them from everything around them — a masked language model (MLM). Add a secondary next-sentence-prediction task to teach sentence-pair relationships."
  solution: "Masking is the key trick. Because the model never sees the answer and must recover it from both directions at once, each token's encoding is shaped by its full left-and-right context — genuinely bidirectional. Pretrain this once on a huge unlabeled corpus (Wikipedia + BookCorpus), then attach a small task-specific head and fine-tune on each downstream dataset, which is cheap by comparison."
  outcome: "BERT swept the language-understanding benchmarks of its day (GLUE, SQuAD) and made 'download a pretrained encoder, fine-tune a light head' the default NLP workflow. It became the understanding-first, encoder-only complement to the generation-first, decoder-only GPT line — two branches off the same transformer trunk. Follow-ups (RoBERTa, DeBERTa) showed the pretraining recipe mattered even more than the extra objectives."

build_steps:
  - { title: "1. Tokenize and add special tokens", detail: "Split text into WordPiece subword tokens and prepend a [CLS] token (its final vector summarizes the whole input) with [SEP] separating sentence pairs." }
  - { title: "2. Embed tokens, positions, and segments", detail: "Each token gets a learned embedding plus a positional embedding (order) plus a segment embedding (sentence A vs B). These three are summed into the input vectors." }
  - { title: "3. Mask about 15% of tokens", detail: "Randomly replace chosen tokens with a [MASK] placeholder (most of the time) so the model has something to reconstruct — this is the fill-in-the-blank signal." }
  - { title: "4. Encode with a bidirectional stack", detail: "Run the sequence through N transformer-encoder blocks. With no causal mask, every token attends to every other token, left and right — the source of bidirectional context." }
  - { title: "5. Predict the masked words", detail: "At each masked position, project the final vector over the vocabulary and train with cross-entropy against the true token. A next-sentence-prediction head trains the [CLS] vector too." }
  - { title: "6. Fine-tune per task", detail: "Throw away the pretraining heads, attach a small task head (classification on [CLS], span prediction on token outputs), and fine-tune the whole model briefly on labeled data." }

key_equation:
  latex: "\\mathcal{L}_{\\mathrm{MLM}}=-\\sum_{i\\in M}\\log P\\!\\left(x_i \\mid x_{\\setminus M}\\right)"
  plain: "For every masked position, the model outputs a probability for the true word given all the unmasked words around it. The loss is the sum of the negative log-probabilities of those true words — so training pushes the model to make the correct blank-filling as likely as possible, using context from both sides."
  terms:
    - { symbol: "\\mathcal{L}_{\\mathrm{MLM}}", meaning: "the masked-language-model training loss to minimize" }
    - { symbol: "M", meaning: "the set of positions that were masked out (~15% of tokens)" }
    - { symbol: "x_i", meaning: "the true token that was hidden at masked position i" }
    - { symbol: "x_{\\setminus M}", meaning: "all the visible (unmasked) tokens — the two-sided context" }
    - { symbol: "P(x_i \\mid x_{\\setminus M})", meaning: "the model's predicted probability of the correct token given that context" }

strengths:
  - "Deeply bidirectional — each token is encoded using both left and right context, ideal for understanding tasks."
  - "One expensive pretraining amortizes across many cheap fine-tunes, transferring general language knowledge widely."
  - "The [CLS] and per-token outputs plug into lightweight heads for classification, tagging, and span extraction."
  - "Self-supervised: learns from raw unlabeled text, so pretraining data is essentially unlimited."

weaknesses:
  - "Not a generator — the bidirectional, masked objective makes BERT poor at free-form text generation."
  - "The [MASK] token appears only in pretraining, not at inference, creating a train/test mismatch the model must paper over."
  - "Only ~15% of tokens contribute loss per example, so pretraining is sample-inefficient versus next-token prediction."
  - "Next-sentence prediction turned out to add little; RoBERTa dropped it and improved by simply training longer on more data."

diagram: |
  flowchart TD
    IN["Input tokens with 15 percent masked"] --> EMB["Token + position + segment embeddings"]
    EMB --> ENC["Bidirectional transformer encoder (x N)"]
    ENC --> CLS["[CLS] vector -> sentence-level tasks"]
    ENC --> TOK["Masked-position vectors"]
    TOK --> MLM["Predict the hidden words (softmax over vocab)"]
    CLS --> HEAD["Fine-tune a small task head"]
diagram_caption: "BERT masks ~15% of tokens, encodes the sequence with both-sides context, and predicts the blanks; after pretraining, a light head is fine-tuned per task."

notable_models:
  - { name: "BERT-base / BERT-large", year: 2018, note: "The original — proved masked, bidirectional pretraining sets a new state of the art across NLP understanding benchmarks." }
  - { name: "RoBERTa", year: 2019, note: "Same architecture, better recipe — dropped next-sentence prediction and trained longer on more data; showed pretraining setup dominates." }
  - { name: "DistilBERT", year: 2019, note: "Distilled BERT to ~40% fewer parameters while keeping ~97% of performance — proved these encoders compress well." }
  - { name: "DeBERTa", year: 2020, note: "Disentangled content/position attention and a better mask decoder pushed the encoder line past human GLUE baselines." }
  - { name: "Sentence-BERT", year: 2019, note: "Adapted BERT to produce comparable sentence embeddings, making it practical for semantic search and clustering." }

resources:
  - { kind: paper, title: "BERT: Pre-training of Deep Bidirectional Transformers (Devlin et al., 2018)", url: "https://arxiv.org/abs/1810.04805" }
  - { kind: blog, title: "The Illustrated BERT, ELMo, and co. (Jay Alammar)", url: "https://jalammar.github.io/illustrated-bert/" }
  - { kind: blog, title: "Generalized Language Models (Lilian Weng)", url: "https://lilianweng.github.io/posts/2019-01-31-lm/" }
  - { kind: tutorial, title: "Hugging Face NLP Course — Transformer models", url: "https://huggingface.co/learn/nlp-course/chapter1/1" }

learn_first:
  - { label: "The Transformer (encoder half)", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "GPT (the decoder-only complement)", href: "/models/architecture/gpt", tag: "Architecture" }
  - { label: "Softmax & cross-entropy loss", href: "/learn/concepts/softmax", tag: "Math" }
  - { label: "Subword tokenization (WordPiece/BPE)", href: "/learn/concepts/tokenization", tag: "Concept" }

tags: [bert, encoder, masked-lm, pretraining, llm]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/1810.04805", title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)", trust: 0.95 }
  - { url: "https://jalammar.github.io/illustrated-bert/", title: "The Illustrated BERT (Jay Alammar)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1907.11692", title: "RoBERTa: A Robustly Optimized BERT Pretraining Approach (Liu et al., 2019)", trust: 0.9 }
---

## What Changed
The transformer gave a powerful architecture, but early language models still read text left-to-right,
so a word's representation could only depend on what came before it. BERT keeps just the transformer's
**encoder** and trains it with **masked language modeling (MLM)**: randomly hide ~15% of tokens and ask
the model to reconstruct them from context on *both* sides.

This forces genuinely bidirectional representations — each token is encoded in light of its full
surroundings — which is exactly what understanding tasks (classification, entailment, question
answering) need. Just as important was the recipe it cemented: pretrain once on a huge unlabeled
corpus, then **fine-tune** a small task-specific head on each downstream dataset.

BERT swept the NLP benchmarks of its day and made "download a pretrained encoder and fine-tune" the
default workflow. It sits on the transformer branch; its encoder-only, understanding-first design is
the complement to the decoder-only, generation-first GPT line.

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
