---
id: hyperparameter/precision
type: hyperparameter
slug: precision
schema_version: 1.0
title: "Training Precision (bf16 / fp16)"
name: bf16
context: training
priority: 10
importance: important
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "fp32 | fp16 | bf16 (mixed precision)"
starting_value: "bf16 = true (on Ampere/Hopper GPUs and TPUs)"
controls: "The numeric format used for the forward/backward pass — trading precision for memory and throughput."
if_increased: "Higher precision (fp32) is most numerically stable but ~2× the memory and much slower."
if_decreased: "Lower precision (bf16/fp16) roughly halves memory and speeds up matmuls, at some numeric range/precision cost."
common_mistake: "Using fp16 without loss scaling, or on hardware where bf16 is available — fp16's tiny exponent range overflows to NaN, while bf16 matches fp32's range and needs no loss scaler."
tags: [training, precision, mixed-precision, memory]
updated: 2026-07-17
sources:
  - { url: "https://arxiv.org/abs/1710.03740", title: "Mixed Precision Training (Micikevicius et al., 2018)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/perf_train_gpu_one", title: "Hugging Face — efficient training on a single GPU", trust: 0.85 }
related: [hyperparameter/gradient-clipping, hyperparameter/effective-batch-size]
---

## Definition
Training precision sets the floating-point format for activations and gradients. **Mixed precision**
keeps a master copy of the weights in fp32 while running the compute-heavy matmuls in a 16-bit format —
either `fp16` (5 exponent bits, 10 mantissa) or `bf16` (8 exponent bits, 7 mantissa).

## What to look for
`bf16=true` is the modern default on Ampere (A100), Hopper (H100), and TPUs. bf16 has the **same
exponent range as fp32**, so it rarely overflows and needs no loss scaling — the trade is fewer mantissa
bits (less precision). `fp16` has more mantissa bits but a narrow exponent range, so it requires dynamic
loss scaling to avoid gradient underflow/overflow and is mainly for older GPUs (V100, T4) that lack bf16.
Never enable both; pick one 16-bit format.

## Calculation
Memory for parameters, gradients, and Adam states in a bf16 mixed-precision setup, per parameter:
$$\underbrace{2}_{\text{bf16 weight}} + \underbrace{2}_{\text{bf16 grad}} + \underbrace{4}_{\text{fp32 master}} + \underbrace{4+4}_{\text{Adam }m,\,v} = 16\ \text{bytes/param}.$$
Pure fp32 training is 4 (weight) + 4 (grad) + 4 + 4 = 16 bytes too, but doubles activation memory and
halves matmul throughput versus 16-bit compute.

## Example
For a 7B model, the optimizer + weight state above is $7\text{e}9 \times 16 = 112$ GB — already beyond a
single 80 GB A100 (hence sharding/ZeRO). The payoff of bf16 is on **activations and throughput**: bf16
matmuls run roughly 2× faster than fp32 on an A100's tensor cores and halve activation memory, which is
often what lets a given micro-batch fit at all. Choosing fp16 on that same A100 would add a loss scaler
and NaN risk for no benefit — bf16 is strictly preferable where supported.
