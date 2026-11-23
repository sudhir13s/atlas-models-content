---
id: fine-tuning-method/qlora
type: fine-tuning-method
slug: qlora
schema_version: 2.8
title: "QLoRA (Quantized Low-Rank Adaptation)"
method_type: peft
memory_profile: low
summary: "Store the frozen model in 4-bit numbers and train LoRA adapters through it, so fine-tuning a 65-billion-parameter model fits on one 48 GB GPU."
when_to_use: "When the frozen model in 16-bit numbers does not fit in GPU memory but its 4-bit copy with LoRA adapters does."
related_hyperparameters: [hyperparameter/lora-r, hyperparameter/lora-alpha, hyperparameter/target-modules, hyperparameter/learning-rate]
profile:
  trains: "LoRA's two small matrices on every linear layer; the base is frozen in 4-bit NF4"
  models_in_training: "One: the 4-bit frozen model with 16-bit adapters"
  data: "Input and target pairs for the task"
  stages: "One"
  source: "https://arxiv.org/abs/2305.14314"
typical_values:
  - setting: "QLoRA paper: LoRA rank and α, LoRA on every linear layer"
    value: "r = 64, α = 16"
    source: "https://arxiv.org/abs/2305.14314"
  - setting: "QLoRA paper: learning rate, LLaMA 7B and 13B / 33B and 65B (constant schedule)"
    value: "2e-4 / 1e-4"
    source: "https://arxiv.org/abs/2305.14314"
  - setting: "QLoRA paper: LoRA dropout in its Guanaco training, models up to 13B / 33B and 65B (Appendix B.2)"
    value: "0.1 / 0.05"
    source: "https://arxiv.org/abs/2305.14314"
  - setting: "QLoRA paper: Adam β2; maximum gradient norm"
    value: "0.999; 0.3"
    source: "https://arxiv.org/abs/2305.14314"
  - setting: "QLoRA paper: block size of the weights / of the quantization constants"
    value: "64 / 256"
    source: "https://arxiv.org/abs/2305.14314"
  - setting: "QLoRA paper: storage / computation number types"
    value: "4-bit NormalFloat (NF4) / BFloat16"
    source: "https://arxiv.org/abs/2305.14314"
mistakes:
  - mistake: "Adding LoRA to the query and value matrices only"
    why: >-
      With LoRA on the query and value projections, the standard practice from the LoRA paper, the
      QLoRA authors could not match full fine-tuning on large base models. They found the total
      number of adapters the most critical LoRA setting, and LoRA on **all linear layers**
      necessary to match it (their Figure 2, LLaMA 7B on Alpaca).
    fix: "Put adapters on every linear layer of the transformer blocks."
  - mistake: "Shrinking r to save memory"
    why: >-
      For LLaMA 7B at batch size 1 the paper measured LoRA weights of **26 MB** against LoRA input
      gradients of **567 MB** (18 MB per sequence with gradient checkpointing) and a 4-bit base of
      **5,048 MB**. With LoRA on all layers it found r **unrelated to final performance**.
    fix: "Keep a moderate r; use gradient checkpointing, which is where the memory goes."
  - mistake: "Using plain 4-bit floats instead of NF4"
    why: >-
      In the paper's Table 4 (LLaMA 7B to 65B, 5-shot MMLU), 4-bit floats averaged **52.2** and NF4
      with double quantization **53.1**, against **53.0** for 16-bit BFloat16: the authors report FP4
      about one percentage point behind.
    fix: "Use NF4 with double quantization, the paper's setting for every experiment."
  - mistake: "Reading the memory figures as a limit for any batch"
    why: >-
      The paper's Figure 6 totals are for batch size **1**, sequence length **512** and gradient
      checkpointing. It notes that a larger batch or a long sequence can make the activation
      gradients consume a considerable amount of memory, and that the 33B model needs paged
      optimizers to fit 24 GB.
    fix: "Measure at your own batch size and sequence length, and keep paged optimizers on."
visual:
  kind: memory_bars
  panels:
    - title: "Fine-tuning LLaMA 65B"
      bars:
        - { label: "16-bit, full", gigabytes: 780, is_lower_bound: true }
        - { label: "QLoRA", gigabytes: 45.0, is_lower_bound: false }
    - title: "QLoRA training memory by LLaMA size"
      bars:
        - { label: "7B", gigabytes: 6.9, is_lower_bound: false }
        - { label: "13B", gigabytes: 11.3, is_lower_bound: false }
        - { label: "33B", gigabytes: 24.7, is_lower_bound: false }
        - { label: "65B", gigabytes: 45.0, is_lower_bound: false }
  caption: "Published figures from the QLoRA paper: more than 780 GB for regular 16-bit fine-tuning of LLaMA 65B (Introduction), and the total QLoRA training memory per LLaMA size at batch size 1, sequence length 512, with gradient checkpointing (Figure 6)."
tags: [fine-tuning, peft, qlora, quantization, lora]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/2305.14314", title: "QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)", trust: 0.9 }
---

## Definition

**QLoRA is LoRA trained on top of a model whose frozen weights are stored in 4 bits.**
It is from Dettmers et al. (2023), titled Efficient Finetuning of Quantized LLMs.

- **Quantization** stores each number with fewer bits; 4 bits allow only 16 different values.
- **LoRA** trains two small matrices beside each frozen weight matrix; see the
  [[fine-tuning-method/lora]] page.
- **The saving** is in the frozen model: the figure shows fine-tuning LLaMA 65B falling from
  **more than 780 GB** to **45.0 GB**.
- **The claim:** the authors report that this matches **16-bit full fine-tuning** and 16-bit LoRA
  on their academic benchmarks.

## How It Works

**One storage type for the frozen weights, one computation type for the arithmetic.**

1. **Quantize the base once.** The weights are cut into blocks of **64**, and each block is scaled
   by its largest absolute value and stored in **4-bit NormalFloat (NF4)**.
2. **NF4** is a 4-bit type whose 16 values are placed so that each holds an equal share of a normal
   distribution; the authors state pretrained weights are usually zero-centred and normally
   distributed (their Appendix F tests this).
3. **Double quantization.** Each block's scale is itself stored in 8 bits, in blocks of **256**.
4. **Forward and backward pass.** Whenever a weight is used it is turned back into **BFloat16**
   (16-bit) for the multiplication.
5. **Gradients** flow through the frozen 4-bit weights into the **16-bit LoRA adapters**, the only
   weights that are updated.
6. **Paged optimizers.** Optimizer state sits in NVIDIA unified memory, so it moves to CPU memory
   when the GPU runs out and comes back for the update step.

## Calculation

**Double quantization, in bits per weight.** Each block of 64 weights has one scale.

In words: one 32-bit scale per 64 weights, before; one 8-bit scale per 64 weights plus one
32-bit scale per 64 × 256 weights, after.

$$
\text{before: } \frac{32}{64} = 0.5
$$

$$
\text{after: } \frac{8}{64} + \frac{32}{64 \times 256} \approx 0.127
$$

| Symbol | Meaning |
| --- | --- |
| 32 | bits of a 32-bit scale |
| 8 | bits of an 8-bit scale |
| 64 | weights per block |
| 256 | scales per block of the second quantization |

## Worked Example

**How much double quantization saves on a 65B model.** The numbers are the paper's.

1. **Before:** 32 ÷ 64 = **0.5** bits per weight for the scales.
2. **After:** 8 ÷ 64 = 0.125, and 32 ÷ (64 × 256) = 32 ÷ 16,384 ≈ 0.00195; together **0.127** bits.
3. **Saved:** 0.5 − 0.127 = **0.373** bits per weight, the paper's figure.
4. **On 65 billion weights:** 65 × 10⁹ × 0.373 ÷ 8 ≈ 3.03 × 10⁹ bytes, about **3 GB**, as the
   paper states.
5. **The 4-bit weights themselves:** 65 × 10⁹ × 4 ÷ 8 = **32.5 × 10⁹** bytes, before scales.

**What this means:** the scales are small next to the weights, but at 65B their saving is
gigabytes; the authors write that double quantization helps fit 33B and 65B models into 24 and
48 GB GPUs.

## Memory

**The 4-bit base is most of what remains; the adapters are a small part.**

- **Totals** (the figure): **6.9 GB** for LLaMA 7B, **11.3** for 13B, **24.7** for 33B and **45.0**
  for 65B, at batch size 1 and sequence length 512 with gradient checkpointing.
- **A separate 7B measurement** (the paper's section 2, FLAN v2, batch size 1): a 4-bit base of
  **5,048 MB**, LoRA weights of **26 MB**, and LoRA input gradients of **567 MB**, or **18 MB** per
  sequence with gradient checkpointing.
- **Deployed:** the paper's smallest Guanaco model (7B) needs **5 GB**.

## Trade-offs

**The paper reports no loss against 16-bit methods on its benchmarks.**

- **Quality:** on GLUE and Super-NaturalInstructions (RoBERTa-large and T5 up to 3B) 4-bit QLoRA
  replicates 16-bit full fine-tuning (Table 3); on LLaMA 7B to 65B, NF4 with double quantization
  averages **53.1** on MMLU against **53.0** for 16-bit LoRA (Table 4).
- **Not tested at full scale:** the authors compare with full fine-tuning only up to 3B, since
  full fine-tuning at 11B and above needs more than one server.
- **Data over size:** a **9k**-example dataset (OASST1) beat a **450k**-example one (FLAN v2,
  subsampled) on chatbot performance.
- **Speed:** with batch size 16, paged optimizers trained 65B models on 48 GB GPUs at the same
  speed as regular ones; the authors leave measuring slowdowns from paging to future work.

## When To Use

**Use QLoRA when GPU memory is the limit.**

- **Use it** when a model's 16-bit weights do not fit, but its 4-bit copy plus adapters does: the
  paper fine-tunes 65B on one 48 GB GPU.
- **Use plain LoRA** when the 16-bit model fits; it skips the conversion from 4 bits at every use.
- **Check** your own batch size and sequence length: the published totals are for batch size 1
  and 512 tokens.
