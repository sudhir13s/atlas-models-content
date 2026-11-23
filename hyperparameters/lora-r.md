---
id: hyperparameter/lora-r
type: hyperparameter
slug: lora-r
schema_version: 2.2
title: "LoRA Rank (r)"
name: r
context: training
priority: 5
importance: critical
goals: [finetuning]
models: [llm, vision, audio, diffusion]
range: "1 — 64"
controls: "The size of the two small matrices that LoRA trains in place of a large frozen one: a higher rank gives the adapter more numbers to learn, a lower rank fewer."
effects:
  decrease:
    - "Fewer trainable numbers in each adapter, in proportion to r"
    - "A smaller adapter file to store"
    - "Less learning capacity"
    - "In the LoRA paper, a rank as small as 1 sufficed for adapting Wq and Wv on its test tasks"
  increase:
    - "More trainable numbers in each adapter, in proportion to r"
    - "A larger adapter file to store"
    - "More learning capacity"
    - "With the alpha / r scale, the adapter's output is divided by a larger number"
scale:
  min: 1
  max: 256
  typical_min: 1
  typical_max: 64
  spacing: log
typical_values:
  - setting: "Hugging Face PEFT: default of LoraConfig"
    value: "8"
    source: "https://github.com/huggingface/peft/blob/main/src/peft/tuners/lora/config.py"
  - setting: "LoRA paper: GPT-3 175B, the smaller budget of 4.7 million parameters (Wq and Wv adapted)"
    value: "1"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "LoRA paper: GPT-2 on text generation tasks (Wq and Wv adapted)"
    value: "4"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "LoRA paper: RoBERTa base and large, and DeBERTa XXL, on GLUE tasks (Wq and Wv adapted)"
    value: "8"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "Alpaca-LoRA training script: default (q_proj and v_proj adapted)"
    value: "8"
    source: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py"
  - setting: "QLoRA: LLaMA fine-tuning, LoRA on all linear layers"
    value: "64"
    source: "https://arxiv.org/abs/2305.14314"
mistakes:
  - mistake: "Assuming a larger rank always gives a better model"
    why: >-
      In the LoRA paper's Table 6 (GPT-3 175B), adapting Wq and Wv at **r = 64** scored 73.5 on
      WikiSQL against **73.8 at r = 8** and 73.4 at r = 1. The authors conclude that a **rank as
      small as one suffices** for adapting Wq and Wv on those datasets. QLoRA reports that **LoRA r
      is unrelated to final performance** when LoRA is used on all layers.
    fix: >-
      Start from a small rank such as the PEFT default of 8, and raise it only when a comparison on
      your own evaluation shows a gain.
  - mistake: "Spending the parameter budget on rank instead of on more weight matrices"
    why: >-
      With the same budget of 18 million parameters on GPT-3, the LoRA paper's Table 5 gives
      **70.4** on WikiSQL for Wq alone at r = 8, and **73.7** for Wq and Wv at r = 4. QLoRA calls the
      total number of LoRA adapters the most critical LoRA hyperparameter.
    fix: >-
      Spread the adapters over more matrices before raising the rank; see the target modules page.
  - mistake: "Raising r and leaving lora_alpha unchanged"
    why: >-
      The adapter's output is multiplied by **lora_alpha / r** (LoRA paper; PEFT's default). Doubling
      r with the same alpha halves that multiplier. Kalajdzievski reports that this factor slows
      learning at higher ranks, so that there is **little to no difference** compared with low ranks.
    fix: >-
      Decide alpha together with r (see the LoRA alpha page), or set `use_rslora=True` in PEFT,
      which divides by the square root of r instead.
  - mistake: "Counting the adapter's size as r times the matrix size"
    why: >-
      The two small matrices hold **r × (d + k)** numbers, not r × d × k. For a 12,288 by 12,288
      matrix of GPT-3 at r = 8 that is 196,608 numbers, not 1.2 billion.
    fix: "Use the formula under Calculation, summed over every adapted matrix and layer."
tags: [finetuning, lora, peft, rank]
updated: 2026-11-23
related: [hyperparameter/lora-alpha, hyperparameter/target-modules, hyperparameter/learning-rate]
sources:
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2305.14314", title: "QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2312.03732", title: "A Rank Stabilization Scaling Factor for Fine-Tuning with LoRA (Kalajdzievski, 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2005.14165", title: "Language Models are Few-Shot Learners (Brown et al., 2020)", trust: 0.9 }
  - { url: "https://github.com/huggingface/peft/blob/main/src/peft/tuners/lora/config.py", title: "Hugging Face PEFT source: LoraConfig (defaults)", trust: 0.9 }
  - { url: "https://github.com/huggingface/peft/blob/main/src/peft/tuners/lora/layer.py", title: "Hugging Face PEFT source: LoRA layer (lora_A, lora_B, scaling)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/peft/developer_guides/lora", title: "Hugging Face PEFT documentation: LoRA", trust: 0.9 }
  - { url: "https://huggingface.co/docs/peft/quicktour", title: "Hugging Face PEFT documentation: Quicktour (print_healthcheck)", trust: 0.9 }
  - { url: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py", title: "Alpaca-LoRA training script (finetune.py)", trust: 0.8 }
---

## Definition

The **LoRA rank**, written **r**, is the size of the two small matrices that Low-Rank Adaptation
(LoRA) trains.

- A **matrix** is a table of numbers. Most of a language model's weights (the numbers it learns)
  sit in large matrices; a matrix with $d$ rows and $k$ columns holds $d \times k$ numbers.
- **Fine-tuning** means training an already-trained model further on new data.
- **LoRA** fine-tunes without changing those large matrices. It keeps each one **frozen** and
  trains a small **change** to add to it, built from two thin matrices.
- The **rank** r is the shared inner size of those two thin matrices: one is $d$ by $r$, the other
  $r$ by $k$.

In the Hugging Face PEFT library the setting is the `r` field of `LoraConfig`, and its default is
**8**. The PEFT documentation puts the trade-off in one line: a higher rank means **more
parameters to train**, and also **more learning capacity**.

**Analogy:** describing a picture with a fixed number of brush strokes. One stroke gives only the
broad shape; more strokes allow more detail, and each stroke is something more to paint. The
analogy shows the trade-off only; the exact rule is under Calculation.

## How It Works

The scale above shows the rank on a logarithmic axis (each tick a doubling), with the published
values from the table marked.

### What LoRA trains

The LoRA paper (Hu et al.) writes the change to a frozen weight matrix $W_0$ as the product of two
matrices, $B$ and $A$:

1. $W_0$ has $d$ rows and $k$ columns. It is **frozen**: in the paper's words, it does not receive
   gradient updates.
2. $B$ has $d$ rows and $r$ columns; $A$ has $r$ rows and $k$ columns. Only $A$ and $B$ are
   trained. The paper requires $r$ to be much smaller than both $d$ and $k$.
3. Multiplying $B$ by $A$ gives a $d$ by $k$ matrix, the same shape as $W_0$. That product is the
   change, $\Delta W = BA$.
4. For an input $x$, the layer computes $h = W_0 x + BAx$, with the second part multiplied by
   $\alpha / r$ (the LoRA alpha page covers that factor).
5. At the start, $A$ is filled with random numbers and $B$ with **zeros**, so $BA$ is zero and the
   model begins exactly as it was. PEFT does the same: it fills `lora_B` with zeros.
6. For use after training, the paper computes and stores $W = W_0 + BA$ and runs inference as
   usual, with one matrix per layer as before.

In PEFT's code, `lora_A` is a layer from the input size to `r`, and `lora_B` a layer from `r` to
the output size.

### Why the rank sets the size

A product $BA$ built through an inner size of r can only express changes of **rank r or less**
(rank, in linear algebra, counts how many independent directions a matrix acts in). A small r
limits which changes are possible and keeps the number of trainable values small.

The LoRA paper tested how much rank is needed on GPT-3 175B (its Table 6, validation accuracy):

| Matrices adapted | Task | r = 1 | r = 2 | r = 4 | r = 8 | r = 64 |
| --- | --- | --- | --- | --- | --- | --- |
| Wq | WikiSQL | 68.8 | 69.6 | 70.5 | 70.4 | 70.0 |
| Wq, Wv | WikiSQL | 73.4 | 73.3 | 73.7 | 73.8 | 73.5 |
| Wq | MultiNLI | 90.7 | 90.9 | 91.1 | 90.7 | 90.7 |
| Wq, Wv | MultiNLI | 91.3 | 91.4 | 91.3 | 91.6 | 91.4 |

How to read the table:

- **Across a row,** the scores barely move from r = 1 to r = 64.
- **Down the rows,** adapting two matrices (Wq and Wv) scores higher than one (Wq) at every rank.
- The authors conclude that a **rank as small as one suffices** for adapting Wq and Wv on these
  datasets.

Two later findings:

- **QLoRA** (Dettmers et al.) reports that **LoRA r is unrelated to final performance** when LoRA
  is applied to all layers, and that the total number of adapters matters most.
- **rsLoRA** (Kalajdzievski) reports that the $\alpha / r$ factor slows learning at higher ranks,
  so that larger ranks show little to no gain; dividing by $\sqrt{r}$ instead is its remedy.

## Calculation

Two things are calculated: **the trainable numbers LoRA adds**, and **how that compares with the
matrix it adapts**.

### 1. Trainable numbers for one matrix

In words: $B$ holds $d \times r$ numbers and $A$ holds $r \times k$; add them.

$$
P = d \times r + r \times k = r\,(d + k)
$$

| Symbol | Meaning |
| --- | --- |
| $d$ | rows of the frozen matrix (its output size) |
| $k$ | columns of the frozen matrix (its input size) |
| $r$ | the LoRA rank |
| $P$ | trainable numbers LoRA adds for this matrix |

### 2. The share of the full matrix

In words: divide the LoRA numbers by the numbers in the full matrix.

$$
\text{share} = \frac{r\,(d + k)}{d \times k}
$$

### 3. The whole model

In words: add up $P$ over every adapted matrix in every layer. When the same $M$ matrices of the
same shape are adapted in each of $L$ layers:

$$
P_\text{total} = L \times M \times r\,(d + k)
$$

| Symbol | Meaning |
| --- | --- |
| $L$ | the number of layers |
| $M$ | the number of adapted matrices per layer |

Steps:

1. Read $d$ and $k$ for each adapted matrix from the model's configuration.
2. Compute $r\,(d + k)$ for each.
3. Multiply by the matrices per layer and by the layers, or add them up one by one when the shapes
   differ.

## Worked Example

### Part A: a toy matrix of 6 rows and 4 columns

Made-up numbers small enough to do by hand: $d = 6$, $k = 4$.

1. The full matrix holds $6 \times 4 = 24$ numbers.
2. **r = 1:** $1 \times (6 + 4) = 10$ numbers. Share: $10 / 24 \approx 0.42$.
3. **r = 2:** $2 \times (6 + 4) = 20$ numbers. Share: $20 / 24 \approx 0.83$.
4. **r = 3:** $3 \times (6 + 4) = 30$ numbers, which is **more** than the 24 of the full matrix.

**What this means:** the count grows in a straight line with r. LoRA saves only while r is small
next to the matrix, which is why the paper asks for r much smaller than $d$ and $k$.

### Part B: one attention matrix of GPT-3 175B

The numbers: GPT-3 175B has a model width of **12,288** (the GPT-3 paper's Table 2.1; the LoRA
paper gives the same full rank). The LoRA paper treats each attention matrix as 12,288 by 12,288.

1. The full matrix: $12{,}288 \times 12{,}288 = 150{,}994{,}944$ numbers.
2. **r = 8:** $8 \times (12{,}288 + 12{,}288) = 8 \times 24{,}576 = 196{,}608$ numbers.
3. Share: $196{,}608 / 150{,}994{,}944 \approx 0.0013$, about **0.13%**.

**What this means:** at r = 8 the adapter for this matrix is about one 768th of its size.

### Part C: checking the LoRA paper's budgets

GPT-3 175B has **96 layers** (GPT-3 paper, Table 2.1). Each attention matrix gives
$r \times 24{,}576$ numbers per layer.

1. **Wq and Wv at r = 1** ($M = 2$): $96 \times 2 \times 1 \times 24{,}576 = 4{,}718{,}592$, the
   paper's **4.7M** budget.
2. **Wq and Wv at r = 8:** $96 \times 2 \times 8 \times 24{,}576 = 37{,}748{,}736$, the paper's
   **37.7M** budget.
3. **One matrix at r = 8** ($M = 1$): $96 \times 1 \times 8 \times 24{,}576 = 18{,}874{,}368$,
   the paper's **18M** budget. Two matrices at r = 4, or four at r = 2, give the same number.

**What this means:** the formula reproduces the paper's own counts. Against the 175 billion
weights of GPT-3, 4.7 million trainable numbers is about 0.003%.

## What To Watch

- **The trainable parameter count.** After wrapping a model, PEFT's `print_healthcheck()` prints
  the trainable and total parameters and the percentage trainable; compare it with the formula
  under Calculation.
- **Your evaluation score at two ranks.** The LoRA paper's Table 6 and QLoRA's finding both show
  scores that barely change with rank; check that a higher rank helps on your task before paying
  for it.
- **lora_alpha.** With the default scale, alpha / r falls as r rises; see the LoRA alpha page.
- **Which matrices are adapted.** The same budget spread over more matrices scored higher in the
  LoRA paper's Table 5; see the target modules page.
