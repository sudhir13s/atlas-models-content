---
id: fine-tuning-method/lora
type: fine-tuning-method
slug: lora
schema_version: 2.8
title: "LoRA (Low-Rank Adaptation)"
method_type: peft
memory_profile: low
summary: "Keep the trained model frozen and learn, for chosen weight matrices, a small change built from two thin matrices."
when_to_use: "When many task-specific versions of one large model are needed and storage, training memory or switching between tasks must stay small."
related_hyperparameters: [hyperparameter/lora-r, hyperparameter/lora-alpha, hyperparameter/target-modules, hyperparameter/learning-rate]
profile:
  trains: "Two small matrices per adapted weight matrix; every original weight stays frozen"
  models_in_training: "One: the frozen model with the small matrices added"
  data: "Input and target pairs for the task"
  stages: "One"
  source: "https://arxiv.org/abs/2106.09685"
typical_values:
  - setting: "LoRA paper: GPT-3 175B, the two parameter budgets (Wq and Wv adapted)"
    value: "r = 1 (4.7M) or r = 8 (37.7M)"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "LoRA paper: GPT-3 175B, learning rate (AdamW, batch size 128, 2 epochs)"
    value: "2e-4"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "LoRA paper: GPT-2 on E2E, WebNLG and DART (Wq and Wv adapted)"
    value: "r = 4, α = 32, learning rate 2e-4"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "LoRA paper: how α is chosen"
    value: "set to the first r tried, then not tuned"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "QLoRA paper: LLaMA 7B to 65B, LoRA on every linear layer"
    value: "r = 64, α = 16"
    source: "https://arxiv.org/abs/2305.14314"
mistakes:
  - mistake: "Spending the budget on a higher rank for one matrix"
    why: >-
      With the same **18 million** trainable numbers on GPT-3 175B, the LoRA paper's Table 5 gives
      **70.4** on WikiSQL for Wq alone at r = 8 and **73.7** for Wq and Wv at r = 4. The QLoRA
      authors could not match full fine-tuning on large models with LoRA on the query and value
      matrices only.
    fix: >-
      Spread the adapters over more matrices before raising r; see the target modules and LoRA rank
      pages.
  - mistake: "Expecting memory to fall as much as the trainable numbers"
    why: >-
      On GPT-3 175B the paper cuts trainable parameters about **10,000 times** but training memory
      only about **3 times** (**1.2 TB to 350 GB**): the frozen weights are still held. For LLaMA 7B
      at batch size 1, QLoRA measured **567 MB** of LoRA input gradients against **26 MB** of LoRA
      weights.
    fix: >-
      Budget memory for the frozen model and the activations first; shrinking r saves little.
  - mistake: "Assuming the adapter slows inference"
    why: >-
      The paper computes and stores **W = W₀ + BA** after training, so inference runs one matrix
      per layer, as before. Its stated limitation is different: once merged, inputs for tasks with
      different A and B cannot share one forward pass.
    fix: >-
      Merge for a single task; keep the adapters separate when one batch must serve several tasks.
visual:
  kind: adapter_size
  rows: 12288
  columns: 12288
  ranks: [1, 2, 4, 8, 64]
  caption: "One 12,288 × 12,288 attention matrix of GPT-3 175B, the size the LoRA paper treats each of Wq, Wk, Wv and Wo as, against LoRA's two matrices at five ranks. Computed from r × (d + k); the ranks are the ones in the paper's Table 6."
tags: [fine-tuning, peft, lora, adapters]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2305.14314", title: "QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)", trust: 0.9 }
---

## Definition

**LoRA freezes a trained model and learns only a small change to some of its weight matrices.**
It is from Hu et al. (2021), titled Low-Rank Adaptation (LoRA).

- **A weight matrix** is a table of numbers the model learned; a matrix of d rows and k columns
  holds d × k numbers.
- **Frozen** means a number is never changed during training.
- **The change** is built from two thin matrices, B and A, whose product has the shape of the
  frozen matrix. Only B and A are trained.
- **The rank r** is the inner size of B and A. The figure shows why it matters: at r = 8, the
  pair for one GPT-3 matrix holds **0.13%** of the matrix's numbers.

**Analogy:** a printed map with a transparent sheet laid over it. You draw your corrections on the
sheet, and the map underneath stays as it was. The analogy shows the idea only; the exact rule is
under Calculation.

## How It Works

**Each adapted matrix gets a side path whose output is added to its own.**

1. **Pick the matrices.** The paper adapts attention matrices only, mostly the query (Wq) and
   value (Wv) projections, and freezes the rest.
2. **Add B and A.** For a frozen matrix W₀ of d × k, B has d rows and r columns and A has r rows
   and k columns, with r much smaller than d and k.
3. **Start at zero.** A starts as random numbers and B as **zeros**, so BA is zero and the model
   begins exactly as it was.
4. **Forward pass.** For an input x the layer computes **h = W₀x + BAx**, with BAx multiplied by
   **α / r**, a constant scale.
5. **Train.** Only A and B receive gradients, so the optimiser keeps state for them alone.
6. **After training.** Either keep B and A as a small separate file, or merge them:
   **W = W₀ + BA**, one matrix per layer as before.

**Why it can work:** the authors hypothesise that the change a task needs has a low "intrinsic
rank". On GPT-3 175B they found a rank as small as **1** sufficed for adapting Wq and Wv on their
test tasks (Table 6).

## Calculation

**Two numbers: what LoRA adds for one matrix, and what it adds for the whole model.**

In words: B holds d × r numbers and A holds r × k; add them.

$$
P = d \times r + r \times k = r\,(d + k)
$$

In words: when the same M matrices of the same shape are adapted in each of L layers, multiply.

$$
P_\text{total} = L \times M \times r\,(d + k)
$$

| Symbol | Meaning |
| --- | --- |
| $d$, $k$ | rows and columns of the frozen matrix |
| $r$ | the LoRA rank |
| $P$ | trainable numbers LoRA adds for one matrix |
| $L$, $M$ | layers, and adapted matrices per layer |

## Worked Example

**Part A: one tiny layer by hand.** All numbers are an example, not from a trained model.

- **Frozen matrix** W₀ (3 rows, 2 columns): rows (1, 0), (0, 1), (1, 1). **Input** x = (2, 1).
- **LoRA pair** with r = 1: A = (0.5, 0.5), one row; scale α / r = 2 / 1 = **2**.

1. **Frozen path:** W₀x = (1·2 + 0·1, 0·2 + 1·1, 1·2 + 1·1) = **(2, 1, 3)**.
2. **Side path, first half:** Ax = 0.5·2 + 0.5·1 = **1.5**.
3. **At the start** B = (0, 0, 0), so BAx = 0 and **h = (2, 1, 3)**, the frozen model's output.
4. **After training** suppose B = (0.2, 0, −0.1). Then BAx = (0.3, 0, −0.15), and times the scale
   2 that is (0.6, 0, −0.3).
5. **Output:** h = (2 + 0.6, 1 + 0, 3 − 0.3) = **(2.6, 1, 2.7)**.
6. **Merge:** W = W₀ + 2·BA has rows (1.2, 0.2), (0, 1), (0.9, 0.9), and Wx = **(2.6, 1, 2.7)**,
   the same output with no side path.
7. **Count:** the matrix holds 3 × 2 = **6** numbers, the pair 1 × (3 + 2) = **5**.

**What this means:** the side path changes the output without touching W₀, and merging removes
it. At this toy size LoRA barely saves anything; the saving comes from large matrices.

**Part B: one GPT-3 matrix** (what the figure draws). The paper treats each attention matrix of
GPT-3 175B as 12,288 × 12,288.

1. **Full matrix:** 12,288 × 12,288 = **150,994,944** numbers.
2. **r = 8:** 8 × (12,288 + 12,288) = 8 × 24,576 = **196,608** numbers.
3. **Share:** 196,608 ÷ 150,994,944 ≈ **0.13%**.

**Part C: the paper's budgets.** GPT-3 175B has **96** layers; adapting Wq and Wv gives M = 2.

1. **r = 1:** 96 × 2 × 1 × 24,576 = **4,718,592**, the paper's **4.7M**.
2. **r = 8:** 96 × 2 × 8 × 24,576 = **37,748,736**, the paper's **37.7M**.
3. **r = 4:** 96 × 2 × 4 × 24,576 = **18,874,368**, the paper's **18M** budget, which it says is
   roughly **35MB** stored in 16-bit numbers.

**What this means:** the formula reproduces the paper's own counts, and even the larger budget is
about **0.02%** of GPT-3's 175 billion weights.

## Memory

**LoRA removes gradients and optimiser state for the frozen weights, not the weights themselves.**

- **Training, GPT-3 175B:** the paper reports **1.2 TB** of GPU memory for full fine-tuning and
  **350 GB** with LoRA, a cut of up to two thirds.
- **Storage:** with r = 4 on Wq and Wv the checkpoint shrinks from **350 GB to 35 MB**. A hundred
  adapted models need about 350 GB + 100 × 35 MB ≈ **354 GB**, against **35 TB** for a hundred
  full copies.
- **What remains:** the frozen weights and the activations. For LLaMA 7B at batch size 1, QLoRA
  measured LoRA weights of **26 MB**, LoRA input gradients of **567 MB**, and a 4-bit base model
  of **5,048 MB**.

## Trade-offs

**The paper reports quality on par with full fine-tuning at a fraction of the trained numbers.**

- **Quality:** on GPT-3 175B, LoRA with 4.7M trained numbers scored **73.4** on WikiSQL and
  **91.7** on MultiNLI-matched, against **73.8** and **89.5** for full fine-tuning (Table 4).
- **Speed:** training throughput on GPT-3 175B rose from **32.5** to **43.1** tokens per second per
  V100 GPU, the paper's 25% speed-up.
- **No added latency once merged,** unlike adapter layers, which the paper measured adding up to
  **30%** latency at batch size 1 on GPT-2 medium (Table 1).
- **Limit:** with A and B merged into W, one forward pass cannot mix inputs for different tasks.
- **Scope of the study:** the paper adapts attention matrices only and leaves the MLP layers to
  future work; QLoRA later applies LoRA to every linear layer.

## When To Use

**Use LoRA when the sources' advantages match your constraint.**

- **Use it** to keep many task versions of one large model: each costs megabytes, not a full copy.
- **Use it** when training memory is short: the paper cut it by up to two thirds on GPT-3 175B.
- **Use QLoRA instead** when even the frozen 16-bit model does not fit; see the QLoRA page.
- **Do not expect** one merged model to serve several tasks in one batch; keep the adapters
  separate for that.
- **Do not expect** a large memory saving at small model sizes: as Part A shows, the pair saves
  numbers only when r is small next to the matrix.
