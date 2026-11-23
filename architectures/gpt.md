---
id: architecture-era/gpt
type: architecture-era
slug: gpt
schema_version: 3.1
title: GPT (Decoder-Only Autoregressive LMs)
name: GPT
era: scaling
era_range: 2018–2020
year: 2020
family: llm
core_innovation: Decoder-only autoregressive pretraining that, scaled up, yields in-context (few-shot) learning without task-specific fine-tuning.
description: A Transformer that keeps only a decoder stack and is trained to predict the next token of a text from the tokens before it; it writes text by repeating that prediction one token at a time.
key_features:
- Decoder-only transformer
- Causal (left-to-right) language modeling
- Generative pretraining on unlabeled text
- Generation one token at a time
paper_title: Language Models are Few-Shot Learners (GPT-3)
paper_url: https://arxiv.org/abs/2005.14165
influenced_by:
- architecture-era/transformer
led_to:
- architecture-era/llama-recipe
- architecture-era/scaling-laws
- architecture-era/rlhf
analogy:
  title: Predictive autocomplete
  body: 'This is an analogy, not the mechanism. Your phone keyboard suggests the next word from the words you just typed. GPT does the same job, always guessing the next token, but it reads everything written so far and was trained on far more text. Show it a few English words, each followed by its French translation, then one more English word, and a model of this kind continues the pattern; the GPT-3 paper illustrates its test settings with exactly this kind of English-to-French prompt. Unlike a keyboard, the model is not looking words up in a list: it computes a probability for every token in its vocabulary, as the worked example shows.'
narrative:
  motivation: '**Labelled data was scarce; unlabelled text was abundant.** The first GPT paper is by **Radford et al. (2018)** at OpenAI.


    - **The problem:** the authors write that large unlabelled text corpora are **abundant**, but labelled data for specific tasks is **scarce**.

    - **Two open questions:** which training objective learns text representations that transfer best, and how to transfer them to a new task without building a new architecture each time.'
  approach: '**Pre-train a Transformer decoder to predict the next token, then adapt it.**


    - **Model:** a **12-layer decoder-only** Transformer with masked self-attention, **768**-number vectors and **12** attention heads.

    - **Data:** **BooksCorpus**, over **7,000** unpublished books, chosen because it has long stretches of contiguous text.

    - **Adapting:** fine-tune on each task with **one added linear output layer**; inputs such as sentence pairs are joined into one token sequence with a delimiter token.'
  solution: '**Scale the same model and stop fine-tuning.**


    - **GPT-2 (Radford et al., 2019):** **1.5B** parameters (1,542M in its Table 2), **48** layers, trained on **WebText**, slightly over **8 million** web documents and **40 GB** of text; tasks are done **zero-shot**, with no parameter or architecture change.

    - **GPT-3 (Brown et al., 2020):** **175B** parameters and **96** layers, every model size trained on **300 billion** tokens; tasks are given as text with **zero, one or a few** demonstrations and **no gradient updates**.

    - **One architectural change:** GPT-3 keeps GPT-2''s model except that its layers **alternate dense and locally banded sparse attention** patterns.'
  outcome: '**Results the authors report:**


    - **GPT:** improved the state of the art on **9 of 12** tasks studied, for example **+8.9%** on the Story Cloze Test and **+5.7%** on RACE.

    - **GPT-2:** state of the art on **7 of 8** language-modelling datasets zero-shot, and **55 F1** on CoQA without using its **127,000+** training examples.

    - **GPT-3:** **85.0 F1** on CoQA and **71.2%** on TriviaQA few-shot; it still struggles on **ANLI** and on reading comprehension sets such as **RACE** and **QuAC**.


    **What came next:**


    - **InstructGPT (Ouyang et al., 2022):** GPT-3 fine-tuned with human feedback; in the authors'' human evaluations, outputs of the **1.3B** InstructGPT model were preferred to those of the **175B** GPT-3.'
key_equation:
  latex: p(x_1,\dots,x_T)=\prod_{t=1}^{T} p\!\left(x_t \mid x_{<t}\right)
  plain: 'In words: the probability of a whole text is the probability of its first token, times the probability of the second given the first, and so on to the end. This is how the GPT-2 paper writes language modelling (its equation 1). Training makes the actual next token as likely as possible; taking the negative logarithm turns the product into a sum of per-token losses, which the worked example computes for three tokens.'
  terms:
  - symbol: x_1,\dots,x_T
    meaning: the tokens of one text, in order from left to right
  - symbol: x_t
    meaning: the token at position t, the one being predicted
  - symbol: x_{<t}
    meaning: 'every token before position t: the only context the causal mask lets the model use'
  - symbol: p(x_t \mid x_{<t})
    meaning: 'the model''s probability for token x_t given the tokens before it: one entry of the softmax over the vocabulary'
  - symbol: \prod
    meaning: multiply the terms for every position t from 1 to T
strengths:
- '**No labels needed to pre-train:** the objective is next-token prediction on raw text; GPT was pre-trained on **BooksCorpus**, over **7,000** unpublished books.'
- '**A task can be given as text:** GPT-3 was applied to every task **without gradient updates**; on TriviaQA it scored **64.3%** zero-shot, **68.0%** one-shot and **71.2%** few-shot, the last state of the art against fine-tuned closed-book systems, the authors report.'
- '**Gains with size:** the GPT-2 authors report that performance improves in a **log-linear** fashion with model capacity; the GPT-3 authors find the gap between zero-, one- and few-shot results **often grows** with model size.'
- '**One network to score and to write:** the GPT-3 authors chose autoregressive models because it is **straightforward both to sample from them and to compute likelihoods** with them.'
weaknesses:
- '**Left context only:** the GPT-3 authors note their design may cost performance on tasks that benefit from **bidirectionality**, such as fill-in-the-blank and comparing two pieces of text; few-shot GPT-3 does **little better than chance** on WiC and ANLI.'
- '**Not aligned by default:** the InstructGPT authors write that making models bigger does not inherently make them better at following a user''s intent; large models can produce **untruthful, toxic or unhelpful** outputs.'
- '**Flaws in long text:** GPT-3 samples sometimes **repeat themselves** semantically, **lose coherence** over long passages and **contradict themselves**, its authors report.'
- '**Cost and data hunger:** the GPT-3 authors call models of its scale **expensive and inconvenient** to run, and note that pre-training sees **much more text** than a human sees in a lifetime.'
visual:
  kind: distribution
  tokens:
  - the
  - cat
  - sat
  - 'on'
  logits:
  - 1.0
  - 2.0
  - 1.5
  - 0.5
  truncate:
    remove:
    - sat
    - 'on'
  panel_titles:
  - 'Without the mask: all 4 positions'
  - 'With the causal mask: later positions removed'
  caption: 'The attention weights of the token "cat" over the four positions of "the cat sat on", computed from its example scores 1.0, 2.0, 1.5 and 0.5. Left: a softmax over all four scores. Right: the causal mask sets the scores of the later tokens "sat" and "on" to minus infinity, so the weights are shared by "the" and "cat" alone. The scores are an example, not from a trained model.'
comparison:
  before: Fine-tuning
  after: Few-shot prompting (GPT-3)
  rows:
  - aspect: Are the model's weights changed for a new task?
    before: 'Yes: the pre-trained model is trained further on a dataset for that task'
    after: 'No: no weight updates are allowed'
    source: https://arxiv.org/abs/2005.14165
  - aspect: Examples of the task needed
    before: Typically thousands to hundreds of thousands of labelled examples
    after: 'K examples, typically 10 to 100: as many as fit in the 2,048-token context'
    source: https://arxiv.org/abs/2005.14165
  - aspect: How the task reaches the model
    before: Through training on its supervised dataset
    after: 'As text: K examples of context and completion, then one context for the model to complete'
    source: https://arxiv.org/abs/2005.14165
  - aspect: Main advantage the authors name
    before: Strong performance on many benchmarks
    after: A major reduction in the need for task-specific data
    source: https://arxiv.org/abs/2005.14165
  - aspect: Main drawback the authors name
    before: A new large dataset for every task, and possible poor generalisation outside it
    after: Results so far much worse than state-of-the-art fine-tuned models
    source: https://arxiv.org/abs/2005.14165
key_numbers:
  columns:
  - Model
  - Parameters
  - Layers
  - Vector width (d_model)
  - Attention heads
  - Context (tokens)
  rows:
  - cells:
    - GPT (2018)
    - not given in the paper
    - '12'
    - '768'
    - '12'
    - '512'
    source: https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf
  - cells:
    - GPT-2, smallest (2019)
    - 117M (the size of the original GPT)
    - '12'
    - '768'
    - not given in the paper
    - 1,024
    source: https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf
  - cells:
    - GPT-2, largest (2019)
    - 1,542M (1.5B)
    - '48'
    - 1,600
    - not given in the paper
    - 1,024
    source: https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf
  - cells:
    - GPT-3 Small (2020)
    - 125M
    - '12'
    - '768'
    - '12'
    - 2,048
    source: https://arxiv.org/abs/2005.14165
  - cells:
    - GPT-3 175B (2020)
    - 175.0B
    - '96'
    - 12,288
    - '96'
    - 2,048
    source: https://arxiv.org/abs/2005.14165
misunderstandings:
- misunderstanding: GPT-3 was fine-tuned on each task it was tested on.
  truth: 'For every task the authors applied GPT-3 **without any gradient updates or fine-tuning**: the task and its demonstrations were given purely as text. Few-shot means **K examples**, typically **10 to 100**, as many as fit in the **2,048-token** context window. The authors left fine-tuning GPT-3 to future work.'
  source: https://arxiv.org/abs/2005.14165
- misunderstanding: Few-shot results prove that the model learns a new task from scratch while reading the prompt.
  truth: 'The GPT-3 authors call this an **open question**: few-shot learning may learn a task anew at inference time, or **recognise a task learned during training**. They suggest synthetic tasks such as unscrambling words are likely learned anew, while translation must have been learned during pre-training.'
  source: https://arxiv.org/abs/2005.14165
- misunderstanding: A bigger GPT automatically follows instructions better.
  truth: The InstructGPT authors write that making language models bigger **does not inherently** make them better at following a user's intent. After fine-tuning with human feedback, outputs of a **1.3B** InstructGPT model were preferred to those of the **175B** GPT-3, which has over **100 times** more parameters.
  source: https://arxiv.org/abs/2203.02155
- misunderstanding: GPT was built to generate text, not to understand it.
  truth: The first paper is titled **Improving Language Understanding by Generative Pre-Training**. Generative pre-training was the means; the model was then fine-tuned on **natural language inference, question answering, semantic similarity and classification**, improving the state of the art on **9 of 12** tasks.
  source: https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf
diagram: "flowchart TD\n  P[\"Prompt tokens\"] --> EMB[\"Token + position embeddings\"]\n  EMB --> DEC[\"Masked (causal) decoder blocks x N\"]\n  DEC --> LOGITS[\"Next-token distribution over vocab\"]\n  LOGITS --> SAMPLE[\"Pick one token\"]\n  SAMPLE --> APPEND[\"Append to sequence\"]\n  APPEND -->|feed back| EMB\n  SAMPLE --> OUT[\"Generated text\"]\n"
diagram_caption: GPT reads the tokens so far through a stack of masked (causal) decoder blocks and scores every vocabulary entry for the next token; to write text it picks one token, appends it and repeats.
notable_models:
- name: GPT
  year: 2018
  note: Generative pre-training of a 12-layer decoder-only Transformer, then fine-tuning on each task; it improved the state of the art on 9 of the 12 tasks studied.
- name: GPT-2
  year: 2019
  note: 1.5B parameters trained on WebText; state of the art on 7 of 8 language-modelling datasets in a zero-shot setting.
- name: GPT-3
  year: 2020
  note: '175B parameters, evaluated zero-, one- and few-shot: tasks and demonstrations given as text, with no gradient updates or fine-tuning.'
- name: InstructGPT
  year: 2022
  note: GPT-3 fine-tuned on labeler demonstrations and then with reinforcement learning from human feedback; outputs of the 1.3B model were preferred to those of the 175B GPT-3.
- name: LLaMA
  year: 2023
  note: 7B to 65B models trained only on publicly available data; LLaMA-13B outperforms GPT-3 (175B) on most benchmarks, per the authors.
resources:
- kind: paper
  title: Language Models are Few-Shot Learners (Brown et al., 2020)
  url: https://arxiv.org/abs/2005.14165
- kind: video
  title: Let's build GPT from scratch (Andrej Karpathy)
  url: https://www.youtube.com/watch?v=kCc8FmEb1nY
- kind: blog
  title: The Illustrated GPT-2 (Jay Alammar)
  url: https://jalammar.github.io/illustrated-gpt2/
- kind: tutorial
  title: nanoGPT — minimal GPT training code (Andrej Karpathy)
  url: https://github.com/karpathy/nanoGPT
learn_first:
- label: The Transformer (decoder half)
  id: architecture-era/transformer
  tag: Architecture
- label: BERT (the encoder-only complement)
  id: architecture-era/bert
  tag: Architecture
- label: Scaling laws — how big to train
  id: architecture-era/scaling-laws
  tag: Concept
- label: Softmax, sampling & temperature
  tag: Math
tags:
- gpt
- decoder
- autoregressive
- in-context-learning
- llm
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/2005.14165
  title: Language Models are Few-Shot Learners (Brown et al., 2020)
  trust: 0.95
- url: https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf
  title: Language Models are Unsupervised Multitask Learners (GPT-2, Radford et al., 2019)
  trust: 0.95
- url: https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf
  title: Improving Language Understanding by Generative Pre-Training (GPT, Radford et al., 2018)
  trust: 0.95
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.98
- url: https://arxiv.org/abs/2203.02155
  title: Training language models to follow instructions with human feedback (Ouyang et al., 2022)
  trust: 0.95
- url: https://arxiv.org/abs/2302.13971
  title: 'LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)'
  trust: 0.95
---

## What Changed

The GPT line kept one architecture, a **decoder-only Transformer**, and changed how a task reaches
it. GPT (2018) was **fine-tuned** on each task; GPT-3 (2020) was given the task **as text in its
prompt**, with its weights left unchanged.

How to read the table above (it follows Section 2 of the GPT-3 paper):

- **Fine-tuning** trains the pre-trained model further on labelled examples of one task, so each
  task ends with its own copy of the weights.
- **Few-shot** writes K solved examples into the prompt, then one unsolved one; the model
  completes it by predicting the next tokens.
- **One-shot** gives one example, and **zero-shot** gives only an instruction in plain language.
- **The context window** (2,048 tokens for every GPT-3 size) caps how many examples fit.
- **The trade-off:** few-shot needs far less task data, but the authors write that its results
  had so far been much worse than state-of-the-art fine-tuned models.

## Key Ideas

- **Decoder-only, causal:** a mask lets each token attend only to itself and earlier tokens, so
  one network both scores text and writes it.
- **Next-token prediction:** the only pre-training signal is the probability of the actual next
  token.
- **Scale:** the GPT-3 authors report that scaling up language models greatly improves
  task-agnostic, few-shot performance.
- **In-context learning:** demonstrations in the prompt steer a model whose weights stay fixed.

## How It Works

The figure above is **GPT-2 small**, a catalog model of this architecture; select a block to open
it. Its sizes match the original GPT: **12** layers and vectors of **768** numbers. Follow it from
the bottom.

A **token** is a piece of text, a word or part of a word. A **vector** is a list of numbers.

1. **Tokenizer.** The text is cut into tokens by **byte-pair encoding (BPE)**. GPT-2 runs BPE on
   bytes, starting from **256** base symbols, so any text can be encoded; its vocabulary has
   **50,257** entries. The original GPT used BPE with 40,000 merges.
2. **Token embedding.** Each token is looked up in a learned table and becomes a vector of
   **768** numbers.
3. **Positional encoding (learned).** GPT adds a **learned position embedding** to each token's
   vector, where the original Transformer used fixed sine and cosine values. The context holds
   **512** tokens in GPT, **1,024** in GPT-2 and **2,048** in GPT-3.
4. **LayerNorm, then masked multi-head attention.** GPT-2 moved layer normalisation to the
   **input** of each sub-block. Attention then runs in **12 heads**, each with vectors of
   768 / 12 = **64** numbers. Its scores for later positions are set to **minus infinity** before
   softmax, so each token sees only itself and the tokens before it (the worked example).
5. **"+" (residual), LayerNorm and feed-forward.** The attention output is added back to its
   input. A feed-forward network then works on each position alone: **768 → 3,072 → 768**, with
   the GELU activation (Gaussian Error Linear Unit). Its output is added back too.
6. **Repeat.** Steps 4 and 5 form one block; GPT-2 small stacks **12**, the largest GPT-2 **48**
   and GPT-3 175B **96**. There is **no encoder** and so no attention over an encoder's output:
   GPT's block has just the two sub-layers.
7. **Final LayerNorm.** GPT-2 added one more layer normalisation after the last block.
8. **Output head.** The last vector of each position is turned into **one score per vocabulary
   entry**, and softmax turns the scores into probabilities for the next token. The GPT paper
   reuses the token-embedding matrix for this step.
9. **Train, then generate.** In training, every position is scored against the token that
   actually follows it. To write text, the model picks a next token, appends it and runs again.
   GPT-2's samples use **top-k sampling with k = 40**; GPT-3's free-form answers use **beam
   search** with a beam width of **4**.

## Worked Example

**One masked attention step, one next-token prediction, one sentence score.** All numbers are an
example, not from a trained model; they are small so every step can be checked by hand.

**Part 1: the causal mask** (what the figure draws). The text is "the cat sat on". The token
**"cat"** has these attention scores, already divided by $\sqrt{d_k}$, against the four
positions: the **1.0**, cat **2.0**, sat **1.5**, on **0.5**.

1. **Without a mask** a softmax over all four gives:
   - $e^{1.0} \approx 2.718$, $e^{2.0} \approx 7.389$, $e^{1.5} \approx 4.482$, $e^{0.5} \approx 1.649$;
     the total is 16.238.
   - Weights: **0.167**, **0.455**, **0.276** and **0.102**. "cat" would take 0.276 + 0.102 =
     **0.378** of its blend from words that come **after** it.
2. **With the mask** the scores of "sat" and "on" become minus infinity, and $e^{-\infty} = 0$:
   - The total is $e^{1.0} + e^{2.0} \approx 2.718 + 7.389 = 10.107$.
   - Weights: 2.718 / 10.107 ≈ **0.269** for "the" and 7.389 / 10.107 ≈ **0.731** for "cat".
3. **Blend the values.** Suppose "the" has the value (1, 0) and "cat" has (0, 1). The new vector
   for "cat" is 0.269 × (1, 0) + 0.731 × (0, 1) = **(0.269, 0.731)**: nothing from the future.

**Part 2: predicting the next token.** At the position of "sat", the output head scores a tiny
vocabulary of four entries: on **2.0**, down **1.0**, the **0.0**, cat **−1.0**.

1. **Softmax:** $e^{2.0} \approx 7.389$, $e^{1.0} \approx 2.718$, $e^{0} = 1.000$,
   $e^{-1.0} \approx 0.368$; the total is 11.475.
2. **Probabilities:** on **0.644**, down **0.237**, the **0.087**, cat **0.032**. They add up to 1.
3. **Loss:** the actual next token is "on", so the loss is $-\ln 0.644 \approx$ **0.440**. A
   certain, correct model would score 0; a more doubtful one scores more.
4. **Generating:** picking the most probable entry gives "on", which is appended to the text
   before the model runs again.

**Part 3: the key equation on three tokens.** Suppose the model gives $p(\text{the}) = 0.2$,
$p(\text{cat} \mid \text{the}) = 0.1$ and $p(\text{sat} \mid \text{the cat}) = 0.4$.

1. **Multiply:** 0.2 × 0.1 × 0.4 = **0.008**, the probability of "the cat sat".
2. **Add the losses instead:** $-\ln 0.2 \approx 1.609$, $-\ln 0.1 \approx 2.303$ and
   $-\ln 0.4 \approx 0.916$; the sum is **4.828**, the same as $-\ln 0.008 \approx 4.828$.

**What this means:** the mask keeps every position from seeing the future, so one pass over a
text trains every next-token prediction at once. Writing text is the same prediction, repeated
one token at a time.
