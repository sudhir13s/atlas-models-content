---
id: parameter/activation-function
type: parameter
slug: activation-function
schema_version: 3.1
title: Activation Function (SwiGLU / GELU)
name: hidden_act
priority: 12
importance: optional
applies_to:
- llm
- transformer
- moe
range: silu · gelu · gelu_new · relu
controls: The non-linear function inside each feed-forward block; whether the block is gated, which adds a third matrix, is set by the model's code.
options:
- value: silu
  effect: 'x·σ(x). In Qwen3, Mixtral and DeepSeek-V3 it gates a three-matrix block (SwiGLU): 3 × hidden_size × intermediate_size per layer.'
- value: gelu
  effect: x·Φ(x) with the exact error function, as in BERT; in its two-matrix block, 2 × hidden_size × intermediate_size.
- value: gelu_new
  effect: The tanh approximation of GELU, as in GPT-2; within 0.0005 of the exact GELU between −4 and 4.
- value: relu
  effect: max(0, x), the original Transformer's choice; 0 for every negative input.
typical_values:
- setting: 'Original Transformer: FFN(x) = max(0, xW₁ + b₁)W₂ + b₂ (Vaswani et al.)'
  value: relu
  source: https://arxiv.org/abs/1706.03762
- setting: BERT-base uncased (config.json)
  value: gelu
  source: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
- setting: GPT-2, activation_function (config.json)
  value: gelu_new
  source: https://huggingface.co/openai-community/gpt2/blob/main/config.json
- setting: Qwen3-8B, gated (config.json)
  value: silu
  source: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
- setting: Mixtral-8x7B, gated experts (config.json)
  value: silu
  source: https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json
mistakes:
- mistake: Reading hidden_act as a sign that the block is gated
  why: The key names the function only. Transformers' Qwen3 code applies it to the gate projection and multiplies by the up projection, three matrices; BERT's code applies its gelu between two matrices. The same key can mean either block.
  fix: Read the model's code, or count its feed-forward matrices, to know whether it is gated.
- mistake: Treating gelu and gelu_new as the same function
  why: Transformers maps gelu to the exact x·Φ(x) and gelu_new to the tanh approximation from the GELU paper, which GPT-2 uses. At x = 1 they give **0.8413** and **0.8412**; the gap stays under **0.0005** between −4 and 4.
  fix: Keep the name a checkpoint was trained with.
- mistake: Thinking Swish and SiLU are different functions
  why: The Swish paper defines x·σ(βx) and notes that with β = 1 it equals the SiLU; Transformers maps both swish and silu to SiLU. The GELU paper names x·σ(x) the Sigmoid Linear Unit.
  fix: Read swish and silu as the same function in configurations.
- mistake: Comparing a gated and a plain block at the same intermediate_size
  why: A gated block has three matrices. Shazeer cut d_ff from **3072** to **2048** to match parameters and computation, and then measured log-perplexity **1.636** for SwiGLU against **1.677** for ReLU and **1.679** for GELU (lower is better).
  fix: Match parameters, not intermediate_size, when comparing activations.
related:
- parameter/intermediate-size-ffn
- parameter/moe-expert-ffn-size
- parameter/hidden-size
visual:
  kind: activation_curves
  functions:
  - relu
  - gelu
  - gelu_new
  - silu
  range:
    min: -4
    max: 4
  marks:
  - -2
  - -1
  - 0
  - 1
  - 2
  caption: The four activations as Hugging Face Transformers computes them, over inputs from −4 to 4, with the values at five inputs tabulated. The inputs are an example range, not values from a trained model.
tags:
- llm
- transformer
- activation
- swiglu
- gelu
- ffn
- parameters
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/1606.08415
  title: Gaussian Error Linear Units (GELUs) (Hendrycks & Gimpel, 2016)
  trust: 0.85
- url: https://arxiv.org/abs/1710.05941
  title: Searching for Activation Functions (Ramachandran et al., 2017)
  trust: 0.85
- url: https://arxiv.org/abs/1702.03118
  title: Sigmoid-Weighted Linear Units for Neural Network Function Approximation in Reinforcement Learning (Elfwing et al., 2017)
  trust: 0.85
- url: https://arxiv.org/abs/2002.05202
  title: GLU Variants Improve Transformer (Shazeer, 2020)
  trust: 0.9
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: Qwen3 Technical Report (Qwen Team, 2025)
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/activations.py
  title: 'Hugging Face Transformers source: activations.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'Hugging Face Transformers source: modeling_qwen3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'Hugging Face Transformers source: modeling_bert.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v3/modeling_deepseek_v3.py
  title: 'Hugging Face Transformers source: modeling_deepseek_v3.py'
  trust: 0.9
- url: https://arxiv.org/abs/2401.04088
  title: Mixtral of Experts (Jiang et al., 2024)
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py
  title: 'Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)'
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen3-8B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: BERT-base uncased configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/openai-community/gpt2/blob/main/config.json
  title: GPT-2 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json
  title: Mixtral-8x7B-Instruct-v0.1 configuration (config.json)
  trust: 0.95
---

## Definition

**hidden_act names the non-linear function inside each feed-forward block.** Transformers
describes it as the non-linear activation function in the decoder, for example gelu, relu or silu;
GPT-2's configuration calls it activation_function.

- **Why a function at all:** without one, the block's two matrix products would collapse into a
  single one.
- **relu** passes positive numbers and zeroes negative ones; **gelu** and **silu** do the same
  smoothly and let small negative outputs through.
- **Gated blocks** (SwiGLU) use the function to scale a second projection; see How It Works.
- **The figure** draws all four over the same inputs.

**Analogy:** a dimmer on each number: fully off for strongly negative inputs, nearly full on for
large positive ones, smooth in between for gelu and silu. The analogy shows the idea only.

## How It Works

**Plain block or gated block.**

1. **Plain** (the original Transformer, BERT, GPT-2): widen with one matrix, apply the function,
   narrow with a second matrix.
2. **Gated** (Shazeer's GLU variants; Qwen3, Mixtral): two matrices widen in parallel; the function
   of one, the **gate**, multiplies the other, the **up** projection; a third matrix narrows back.
3. **SwiGLU** is the gated form with SiLU (Swish with β = 1); GEGLU uses GELU. Transformers' Qwen3
   code computes down_proj(act(gate_proj(x)) × up_proj(x)).
4. **The functions:** ReLU max(0, x); GELU x·Φ(x), Φ the standard normal distribution function
   (Hendrycks and Gimpel); SiLU x·σ(x), σ the sigmoid.

## Calculation

**The functions, and what gating does to the block's size.**

In words: GELU weights x by the chance a standard normal value lies below it; SiLU weights it by the
sigmoid; a gated block has one more matrix.

$$
\text{GELU}(x) = x\,\Phi(x) \qquad \text{SiLU}(x) = \frac{x}{1 + e^{-x}} \qquad \text{FFN} = m\,d\,f
$$

| Symbol | Meaning |
| --- | --- |
| $\Phi$ | standard normal cumulative distribution |
| $m$ | feed-forward matrices: 3 gated, 2 plain |
| $d$, $f$ | hidden_size, intermediate_size |

GPT-2's gelu_new is 0.5x(1 + tanh[√(2/π)(x + 0.044715x³)]), the GELU paper's approximation. For
whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The functions at example inputs, then Qwen3-8B's block from its config.json** (the numbers on its
catalog page: hidden_act silu, hidden_size 4096, intermediate_size 12288).

1. **At x = 1:** relu **1**, gelu **0.8413**, gelu_new **0.8412**, silu **0.7311**.
2. **At x = −1:** relu **0**, gelu **−0.1587**, gelu_new **−0.1588**, silu **−0.2689**.
3. **Lowest values:** silu reaches about **−0.278** near x = −1.28; gelu about **−0.170** near
   x = −0.75.
4. **One gated step** (made-up numbers): gate 1, up 2 gives silu(1) × 2 = 0.7311 × 2 ≈ **1.4621**.
5. **Qwen3-8B's block:** 3 × 4096 × 12,288 = **150,994,944** parameters per layer.
6. **For comparison, BERT-base:** gelu in a plain block, 2 × 768 × 3072 = **4,718,592** weights per
   layer.

**What this means:** the four functions differ most for negative inputs; the bigger structural
choice is gating, which adds a third matrix.

## What To Watch

- **The model's code.** It, not hidden_act, decides whether the block is gated.
- **Name variants.** gelu, gelu_new, gelu_pytorch_tanh and silu or swish are separate entries in
  Transformers.
- **intermediate_size.** Compare gated and plain blocks by parameters; see the FFN width page.
