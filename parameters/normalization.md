---
id: parameter/normalization
type: parameter
slug: normalization
schema_version: 3.1
title: Normalization (RMSNorm / LayerNorm)
name: rms_norm_eps
priority: 13
importance: optional
applies_to:
- llm
- transformer
- moe
range: 10⁻¹² — 10⁻⁵
controls: How each layer rescales a token's vector before using it; in RMSNorm models rms_norm_eps is the small number added under the square root.
effects:
  decrease:
  - Closer to dividing by the exact root mean square
  - 'With a mean square near 0, a much larger output: eps is the only thing under the root then'
  - No change in the parameter count
  increase:
  - Outputs slightly smaller; for the figure's vector, a change below 3 × 10⁻⁸ at 10⁻⁶
  - 'A floor under the divisor: never smaller than √eps'
  - No change in the parameter count
scale:
  min: 1.0e-13
  max: 0.0001
  typical_min: 1.0e-12
  typical_max: 1.0e-05
  spacing: log
typical_values:
- setting: BERT-base uncased, LayerNorm (layer_norm_eps, config.json)
  value: 1e-12
  source: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
- setting: Qwen3-8B, RMSNorm (config.json)
  value: 1e-6
  source: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
- setting: DeepSeek-V3, RMSNorm (config.json)
  value: 1e-6
  source: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
- setting: GPT-2, LayerNorm (layer_norm_epsilon, config.json)
  value: 1e-5
  source: https://huggingface.co/openai-community/gpt2/blob/main/config.json
- setting: Mistral-7B-v0.1, RMSNorm (config.json)
  value: 1e-5
  source: https://huggingface.co/mistralai/Mistral-7B-v0.1/blob/main/config.json
mistakes:
- mistake: Thinking RMSNorm is LayerNorm without the bias
  why: It also drops the mean. Zhang and Sennrich's RMSNorm divides by the root mean square and subtracts nothing; they note it equals LayerNorm only when the mean of the inputs is zero. On the figure's vector (mean **5**) the two give different outputs.
  fix: Compare the formulas under Calculation, not only the parameter counts.
- mistake: Counting two norms per layer in every model
  why: 'Qwen3 adds QK-Norm: Transformers'' Qwen3 code has a query and a key RMSNorm of head_dim weights in every attention layer, beside the two norms of hidden_size.'
  fix: Count the norms from the model's code, as in the worked example.
- mistake: Expecting the norms to weigh in the parameter count
  why: 'Qwen3-8B''s norms hold **308,224** parameters, about **0.004%** of its 8,190,735,360. Zhang and Sennrich''s case for RMSNorm is speed: they report running times **7% to 64%** shorter than LayerNorm on their models, at comparable quality.'
  fix: Choose the norm for training behaviour and speed, not size.
related:
- parameter/hidden-size
- parameter/num-hidden-layers
- parameter/head-dim
visual:
  kind: norm_compare
  vector:
  - 2
  - 4
  - 4
  - 4
  - 5
  - 5
  - 7
  - 9
  eps: 1.0e-06
  model: Qwen3-8B
  caption: An example vector of eight numbers (not from a trained model) normalised both ways with gain 1 and bias 0, with the epsilon of Qwen3-8B's config.json, 10⁻⁶. LayerNorm centres it on 0; RMSNorm keeps every number positive and only rescales.
tags:
- llm
- transformer
- normalization
- rmsnorm
- layernorm
- parameters
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/1910.07467
  title: Root Mean Square Layer Normalization (Zhang & Sennrich, 2019)
  trust: 0.9
- url: https://arxiv.org/abs/1607.06450
  title: Layer Normalization (Ba et al., 2016)
  trust: 0.85
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.95
- url: https://arxiv.org/abs/2505.09388
  title: Qwen3 Technical Report (Qwen Team, 2025)
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen3-8B configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: BERT-base uncased configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json
  title: DeepSeek-V3 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/openai-community/gpt2/blob/main/config.json
  title: GPT-2 configuration (config.json)
  trust: 0.95
- url: https://huggingface.co/mistralai/Mistral-7B-v0.1/blob/main/config.json
  title: Mistral-7B-v0.1 configuration (config.json)
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'Hugging Face Transformers source: modeling_qwen3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'Hugging Face Transformers source: modeling_bert.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt2/modeling_gpt2.py
  title: 'Hugging Face Transformers source: modeling_gpt2.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/deepseek_v3/modeling_deepseek_v3.py
  title: 'Hugging Face Transformers source: modeling_deepseek_v3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py
  title: 'Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)'
  trust: 0.9
---

## Definition

**A normalization layer rescales each token's vector to a steady size before a layer uses it.**
Transformers describes rms_norm_eps as the epsilon used by the RMS normalization layers; there is no
key naming the kind of norm, which comes with the model's code.

- **LayerNorm** (Ba et al.): subtract the vector's mean, divide by its standard deviation, then
  apply a learned gain and bias. BERT and GPT-2 use it.
- **RMSNorm** (Zhang and Sennrich): divide by the root mean square, apply a learned gain; no mean,
  no bias. Qwen3 and DeepSeek-V3 use it.
- **Epsilon** is a tiny number added under the square root; models write it as rms_norm_eps or
  layer_norm_eps.
- **The figure** normalises one example vector both ways.

**Analogy:** a volume knob that brings every voice to the same loudness before the mixing desk. The
analogy shows the idea only; the exact rule is under Calculation.

## How It Works

**Measure the vector, divide, rescale.**

1. **Measure.** LayerNorm takes the mean and variance over the hidden_size numbers of one token;
   RMSNorm takes only the mean of the squares.
2. **Divide.** Each number is divided by √(variance + eps) after subtracting the mean (LayerNorm),
   or by √(mean of squares + eps) (RMSNorm), as Transformers' code computes it.
3. **Rescale.** A learned gain per number follows; LayerNorm also adds a learned bias.
4. **Where.** Vaswani et al. normalise after each sub-layer and its residual, LayerNorm(x +
   Sublayer(x)), as BERT's code does; the Qwen3 report uses RMSNorm before each sub-layer
   (pre-normalization), plus a final norm.

## Calculation

**The two formulas and their parameters.**

In words: LayerNorm centres and scales; RMSNorm only scales. Each norm has hidden_size gains;
LayerNorm also has hidden_size biases.

$$
\text{LN}(x)_i = g_i\,\frac{x_i - \mu}{\sqrt{\sigma^2 + \epsilon}} + b_i \qquad \text{RMSNorm}(x)_i = g_i\,\frac{x_i}{\sqrt{\tfrac{1}{n}\sum_j x_j^2 + \epsilon}}
$$

| Symbol | Meaning |
| --- | --- |
| $x$, $n$ | the vector and its length, hidden_size |
| $\mu$, $\sigma^2$ | mean and variance of $x$ |
| $g$, $b$ | learned gain and bias |
| $\epsilon$ | rms_norm_eps or layer_norm_eps |

For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The figure's vector, then Qwen3-8B's norms from its config.json** (the numbers on its catalog
page). The vector (2, 4, 4, 4, 5, 5, 7, 9) is an example, not from a trained model.

1. **Mean:** 40 ÷ 8 = **5**; **variance:** (9 + 1 + 1 + 1 + 0 + 0 + 4 + 16) ÷ 8 = **4**, so the
   standard deviation is **2**.
2. **LayerNorm:** (x − 5) ÷ 2 = (**−1.5, −0.5, −0.5, −0.5, 0, 0, 1, 2**).
3. **Mean of squares:** 232 ÷ 8 = **29**; root mean square ≈ **5.3852**.
4. **RMSNorm:** x ÷ 5.3852 = (**0.3714, 0.7428, 0.7428, 0.7428, 0.9285, 0.9285, 1.2999, 1.6713**).
5. **Epsilon 10⁻⁶** changes these by less than 3 × 10⁻⁸.
6. **Qwen3-8B's norm parameters:** 36 layers × (2 × 4096 + 2 × 128) + 4096 = **308,224**, about
   **0.004%** of its total.

**What this means:** RMSNorm only rescales, so it keeps the vector's signs and its offset from zero;
LayerNorm also removes the mean.

## What To Watch

- **Which epsilon key.** rms_norm_eps in RMSNorm models, layer_norm_eps or layer_norm_epsilon in
  LayerNorm ones.
- **Placement.** Before each sub-layer (Qwen3) or after it (BERT, the original Transformer).
- **Extra norms.** QK-Norm in Qwen3 adds two norms of head_dim to every layer.
