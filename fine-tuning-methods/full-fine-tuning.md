---
id: fine-tuning-method/full-fine-tuning
type: fine-tuning-method
slug: full-fine-tuning
schema_version: 3.1
title: Full Fine-Tuning
method_type: full
memory_profile: high
summary: Start from a trained model and keep training every one of its weights on new task data.
when_to_use: When every weight should be free to change and the memory for training them all, about 16 bytes per weight with mixed-precision Adam, is available.
related_hyperparameters:
- hyperparameter/learning-rate
- hyperparameter/training-duration
- hyperparameter/effective-batch-size
- hyperparameter/weight-decay
- hyperparameter/precision
profile:
  trains: Every weight of the model
  models_in_training: 'One: the model being trained'
  data: Labelled examples for the task
  stages: One
  source: https://arxiv.org/abs/1810.04805
typical_values:
- setting: 'BERT paper: learning rates (Adam) that worked well across its tasks'
  value: 5e-5, 3e-5, 2e-5
  source: https://arxiv.org/abs/1810.04805
- setting: 'BERT paper: batch sizes'
  value: 16, 32
  source: https://arxiv.org/abs/1810.04805
- setting: 'BERT paper: epochs'
  value: 2, 3, 4
  source: https://arxiv.org/abs/1810.04805
- setting: 'LoRA paper: full fine-tuning of GPT-3 175B (AdamW, batch size 128, 2 epochs)'
  value: learning rate 5e-6
  source: https://arxiv.org/abs/2106.09685
- setting: 'ULMFiT: learning rate of each lower layer (discriminative fine-tuning)'
  value: the layer above's ÷ 2.6
  source: https://arxiv.org/abs/1801.06146
mistakes:
- mistake: Sizing memory by the weights alone
  why: 'ZeRO''s authors note that a **1.5B**-parameter GPT-2 needs **3 GB** for its 16-bit weights but at least **24 GB** to train with mixed-precision Adam, which keeps gradients, a 32-bit copy of the weights and two moment estimates. Activations come on top: about **60 GB** for that model at sequence length 1K and batch size 32.'
  fix: Budget 16 bytes per weight plus activations (see Memory); activation checkpointing and sharding across GPUs reduce them.
- mistake: Fine-tuning too aggressively
  why: The ULMFiT authors write that overly aggressive fine-tuning causes **catastrophic forgetting**, losing what pre-training learned, while too cautious fine-tuning converges slowly and overfits. BERT's learning rates are 2e-5 to 5e-5, and the LoRA paper's for GPT-3 175B is 5e-6.
  fix: Search a few small learning rates on a development set, as BERT did; ULMFiT adds lower rates for lower layers and unfreezes them one at a time.
- mistake: Storing one full copy per task
  why: Every fully fine-tuned model has as many parameters as the original. The LoRA authors compute that **100** fine-tuned copies of GPT-3 175B take about **35 TB**, against about **354 GB** for one base plus 100 LoRA adapters.
  fix: When many task versions are needed, consider LoRA, which stores only a small adapter per task.
visual:
  kind: memory_terms
  parameters: 1500000000
  terms:
  - label: 16-bit weights
    bytes: 2
  - label: 16-bit grads
    bytes: 2
  - label: 32-bit weights
    bytes: 4
  - label: momentum
    bytes: 4
  - label: variance
    bytes: 4
  caption: 'The memory for training every weight of a 1.5-billion-parameter model (ZeRO''s GPT-2 example) with mixed-precision Adam, computed as bytes per weight × parameters for each term ZeRO lists: 16-bit weights and gradients, and 32-bit weights, momentum and variance. Activations are not included.'
tags:
- fine-tuning
- full
- sft
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/1810.04805
  title: 'BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (Devlin et al., 2018)'
  trust: 0.9
- url: https://arxiv.org/abs/1801.06146
  title: Universal Language Model Fine-tuning for Text Classification (Howard and Ruder, 2018)
  trust: 0.9
- url: https://arxiv.org/abs/1910.02054
  title: 'ZeRO: Memory Optimizations Toward Training Trillion Parameter Models (Rajbhandari et al., 2019)'
  trust: 0.9
- url: https://arxiv.org/abs/1412.6980
  title: 'Adam: A Method for Stochastic Optimization (Kingma and Ba, 2014)'
  trust: 0.95
- url: https://arxiv.org/abs/2106.09685
  title: 'LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)'
  trust: 0.9
---

## Definition

**Full fine-tuning keeps training every weight of an already-trained model on new data.**

- **Pre-training** teaches a model general skills from a large amount of text.
- **Fine-tuning** then trains it further on a smaller dataset for one task.
- **Full** means nothing is frozen. In the BERT paper's words, the model is initialised with the
  pre-trained parameters and **all of the parameters** are fine-tuned on labelled task data.
- **The cost,** shown in the figure: every weight needs memory for its gradient and optimizer
  state, **24 GB** for a 1.5-billion-parameter model whose 16-bit weights take **3 GB**.

## How It Works

**Ordinary training, starting from pre-trained weights instead of random ones.**

1. **Load** the pre-trained weights, and add a small output layer if the task needs one (BERT
   adds one for classification).
2. **Forward pass** on a batch of task examples, and compute the loss.
3. **Backward pass:** a gradient for **every** weight.
4. **Optimizer step.** Adam keeps two running estimates per weight, the first moment (an average
   of recent gradients) and the second moment (an average of their squares), and updates every
   weight from them.
5. **Repeat** for a few epochs; BERT used 2 to 4.
6. **Keep** the result as a new, complete model; one copy per task.

**Gentler variants:** ULMFiT (Howard and Ruder, 2018) gives lower layers smaller learning rates and
unfreezes layers one at a time, from the last, to avoid catastrophic forgetting.

## Calculation

**Training memory per weight with mixed-precision Adam, as the ZeRO paper counts it.**

In words: add the bytes each weight needs, then multiply by the number of weights.

$$
M = (2 + 2 + 4 + 4 + 4) \times \Psi = 16\,\Psi \ \text{bytes}
$$

| Term | Bytes per weight | Why it is there |
| --- | --- | --- |
| 16-bit weights | 2 | used in the forward and backward pass |
| 16-bit gradients | 2 | one gradient per weight |
| 32-bit weights | 4 | the copy the optimizer updates |
| Momentum | 4 | Adam's first moment |
| Variance | 4 | Adam's second moment |
| $\Psi$ | | the number of weights (parameters) |

Activations and temporary buffers come on top; ZeRO calls them residual states.

## Worked Example

**Three sizes, the same 16 bytes per weight.** The first size is an example.

1. **A small model of 1 million weights:** 16 × 1,000,000 = **16,000,000** bytes, **16 MB**.
2. **GPT-2 with 1.5 billion weights** (the figure): 16 × 1.5 × 10⁹ = **24 × 10⁹** bytes, **24 GB**,
   ZeRO's own figure. By term: 3 + 3 + 6 + 6 + 6 = 24 GB.
3. **The weights alone:** 2 × 1.5 × 10⁹ = **3 GB**, an eighth of the total.
4. **A 7-billion-weight model:** 16 × 7 × 10⁹ = **112 × 10⁹** bytes, **112 GB**, before activations.

**What this means:** the optimizer state, 12 of the 16 bytes, is most of the memory. This is why
methods that freeze most weights, such as LoRA, save memory: frozen weights need no gradient and
no optimizer state.

## Memory

**Everything is trained, so everything needs optimizer state.**

- **Model states:** 16 bytes per weight with mixed-precision Adam, as above.
- **Activations:** about **60 GB** for the 1.5B GPT-2 at sequence length 1K and batch size 32,
  ZeRO reports; activation checkpointing cuts this by recomputing them.
- **Sharding:** ZeRO splits the model states across GPUs. Its example: a 7.5B model needs
  **120 GB** of model states (16 × 7.5) on every GPU with standard data parallelism, and **1.9 GB**
  per GPU with ZeRO's full partitioning across 64 GPUs.
- **Storage:** each fine-tuned model is a full copy, as large as the original.

## Trade-offs

**Full fine-tuning places no limit on which weights change, and pays for it in memory.**

- **Cost of the run:** BERT's authors report every fine-tuning result in their paper reproducible
  in at most **1 hour** on one Cloud TPU, far less than pre-training.
- **Quality:** in the LoRA paper's GPT-3 175B tests, full fine-tuning scored **73.8** on WikiSQL
  and **89.5** on MultiNLI-matched, against **73.4** and **91.7** for LoRA with 4.7M trained
  numbers.
- **Data:** BERT's authors found large datasets (100k+ labelled examples) far less sensitive to
  hyperparameters than small ones.
- **Forgetting:** ULMFiT's authors note that language models overfit small datasets and forget
  when fine-tuned too aggressively.

## When To Use

**Use full fine-tuning when its memory fits and one model per task is acceptable.**

- **Use it** when the hardware holds 16 bytes per weight plus activations, or can shard them.
- **Use it** for a smaller model and one task: BERT's results fine-tuned in about an hour.
- **Consider LoRA or QLoRA** when memory is short or many task versions are needed; see
  [[fine-tuning-method/lora]] and [[fine-tuning-method/qlora]].
- **Use a small learning rate and few epochs,** and check a development set, as BERT did.
