---
id: parameter/context-length
type: parameter
slug: context-length
schema_version: 3.1
title: Context Length (max_position_embeddings)
name: max_position_embeddings
priority: 10
importance: important
applies_to:
- llm
- transformer
- moe
range: 512 — 131,072 tokens
controls: The longest sequence of tokens the model is set up to handle in one pass; the usable context the maker states can differ from the configuration value.
effects:
  decrease:
  - 'Less text fits in one pass: prompt and answer together'
  - 'Attention scores fall with the square of the length: half the length, a quarter of the scores'
  - A smaller KV cache, in a straight line with the length
  - 'With learned positions, fewer parameters: one row per position'
  increase:
  - More text fits in one pass
  - 'Attention scores grow with the square of the length: double the length, four times the scores'
  - A larger KV cache, in a straight line
  - No parameters at all with rotary positions (RoPE); one row per position with learned positions
scale:
  min: 256
  max: 262144
  typical_min: 512
  typical_max: 131072
  spacing: log
typical_values:
- setting: BERT-base, learned positions (config.json)
  value: '512'
  source: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
- setting: GPT-2, context size (GPT-2 paper)
  value: '1024'
  source: https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf
- setting: Qwen3-8B, native context (model card)
  value: '32768'
  source: https://huggingface.co/Qwen/Qwen3-8B
- setting: Qwen3-8B, max_position_embeddings (config.json)
  value: '40960'
  source: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
- setting: Qwen3-8B, with YaRN scaling, validated by Qwen (model card)
  value: '131072'
  source: https://huggingface.co/Qwen/Qwen3-8B
- setting: Llama 3, after long-context pre-training (Llama 3 paper)
  value: 128K
  source: https://arxiv.org/abs/2407.21783
mistakes:
- mistake: Reading max_position_embeddings as the usable context
  why: Transformers describes the key as the maximum length the model "might ever be used with", not the length it was trained or validated for. Qwen3-8B's config.json says **40,960**; its card says it supports **32,768** tokens natively and **131,072** with YaRN, and explains 40,960 as 32,768 tokens reserved for output plus 8,192 for a typical prompt.
  fix: Take the context length from the model card, and the configuration value only as a limit.
- mistake: Turning on context extension for every input
  why: Qwen's card says the common frameworks implement **static** YaRN, whose scaling factor stays the same whatever the input length, potentially impacting performance on shorter texts. It advises against enabling YaRN when the context does not exceed 32,768 tokens.
  fix: Enable YaRN only for inputs longer than the native context, as the card recommends.
- mistake: Assuming a longer context always costs parameters
  why: 'It does only with **learned** positions: BERT''s position table is max_position_embeddings × hidden_size, 512 × 768 = **393,216** parameters. Qwen3 uses rotary positions, computed from a fixed formula into a buffer that holds no trained parameters.'
  fix: Count position parameters only for models with a learned position table.
- mistake: Ignoring the cost of long inputs at run time
  why: 'Self-attention costs grow with the square of the sequence length (Vaswani et al., Table 1), and the Llama 3 team trained on long sequences only in the final stages for this reason. The KV cache grows in a straight line: Qwen3-8B needs **18 GiB** of it at 131,072 tokens in 16-bit.'
  fix: Budget memory for the cache at the length you will actually use.
related:
- parameter/positional-encoding
- parameter/head-dim
- parameter/num-key-value-heads-gqa
visual:
  kind: context_cost
  model:
    name: Qwen3-8B
    vocabulary_size: 151936
    hidden_size: 4096
    intermediate_size: 12288
    num_layers: 36
    attention_heads: 32
    kv_heads: 8
    head_size: 128
    tie_word_embeddings: false
    ffn_matrices: 3
    qk_norm: true
  bytes_per_value: 2
  lengths:
  - 4096
  - 8192
  - 16384
  - 32768
  - 40960
  - 65536
  - 98304
  - 131072
  marks:
  - length: 32768
    label: native (card)
  - length: 131072
    label: YaRN (card)
  caption: 'Computed from Qwen3-8B''s config.json: the attention scores one layer computes (length² × 32 query heads) and the KV cache in 16-bit numbers, at each context length. Marked: the native 32,768 and the 131,072 with YaRN from the model card; the table also lists 40,960, the max_position_embeddings of its config.json.'
tags:
- llm
- transformer
- context
- positions
- rope
- parameters
updated: '2026-11-23'
sources:
- url: https://huggingface.co/Qwen/Qwen3-8B
  title: Qwen3-8B model card
  trust: 0.9
- url: https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json
  title: Qwen3-8B configuration (config.json)
  trust: 0.95
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py
  title: 'Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py
  title: 'Hugging Face Transformers source: modeling_qwen3.py'
  trust: 0.9
- url: https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/modeling_bert.py
  title: 'Hugging Face Transformers source: modeling_bert.py'
  trust: 0.9
- url: https://huggingface.co/google-bert/bert-base-uncased/blob/main/config.json
  title: BERT-base uncased configuration (config.json)
  trust: 0.95
- url: https://arxiv.org/abs/1706.03762
  title: Attention Is All You Need (Vaswani et al., 2017)
  trust: 0.95
- url: https://arxiv.org/abs/2407.21783
  title: The Llama 3 Herd of Models (Llama Team, 2024)
  trust: 0.9
- url: https://arxiv.org/abs/2505.09388
  title: Qwen3 Technical Report (Qwen Team, 2025)
  trust: 0.9
- url: https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf
  title: Language Models are Unsupervised Multitask Learners (GPT-2, Radford et al., 2019)
  trust: 0.9
---

## Definition

**Context length is how many tokens a model can take in one pass, prompt and answer together.**
In a configuration it usually appears as max_position_embeddings, which Transformers describes as
"the maximum sequence length that this model might ever be used with".

- **A token** is a piece of text; the context is every token the model can see when it writes the
  next one.
- **The configuration value is a limit, not a promise.** The context a maker trained and validated
  for is stated in its card, and can differ.
- **Qwen3-8B is the example:** config.json says **40,960**, the card says **32,768** natively and
  **131,072** with YaRN.
- **The figure** shows the run-time cost: attention scores grow with the square of the length, the
  KV cache in a straight line.

**Analogy:** the size of a desk. The furniture catalogue lists the largest desk the frame can take,
but the maker says how much paper you can actually work with on it. The analogy shows the idea only.

## How It Works

**The length limits positions, attention and the cache.**

1. **Positions.** The model must know where each token is. **Learned** positions (BERT) keep one
   trained row per position, max_position_embeddings rows of hidden_size numbers. **Rotary**
   positions (RoPE, Qwen3) rotate queries and keys by a fixed formula and store no trained rows.
2. **Attention.** Each token is scored against every earlier token, in every head of every layer.
3. **Cache.** While writing, the keys and values of every earlier token are kept.
4. **Extension.** Qwen's card describes YaRN, a change to the rotary scaling, set with a factor of
   4.0 on an original 32,768 positions, to reach 131,072.

**Three numbers for one model, Qwen3-8B:**

- **32,768:** the Qwen3 report says all models were pre-trained on sequences of this length.
- **128K:** the report's Table 1 lists it; the report reaches it with YaRN and Dual Chunk Attention,
  a four-fold increase at inference, and the card gives 131,072 with YaRN.
- **40,960:** config.json; the card says it reserves 32,768 tokens for output and 8,192 for a
  typical prompt.

## Calculation

**Three costs of a context of n tokens.**

In words: learned positions cost one row per position; each layer computes n² scores per query
head; the cache keeps a key and a value per key-value head, per layer, per token.

$$
\text{positions} = n_\text{max} \times d \qquad \text{scores} = n^2 \times n_h \qquad \text{cache} = 2\,L\,n_{kv}\,h\,b \times n
$$

| Symbol | Meaning |
| --- | --- |
| $n_\text{max}$ | max_position_embeddings (learned positions only; 0 with RoPE) |
| $n$ | the number of tokens in use |
| $d$, $L$ | hidden_size, num_hidden_layers |
| $n_h$, $n_{kv}$, $h$ | query heads, key-value heads, head_dim |
| $b$ | bytes per cached number: 2 in 16-bit |

For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Qwen3-8B, from its config.json and card** (the context length on its catalog page is the card's
32,768): 36 layers, 32 query heads, 8 key-value heads, head_dim 128, rotary positions.

1. **Position parameters:** **0**; rotary positions are computed, not stored.
2. **Cache per token:** 2 × 36 × 8 × 128 × 2 = **147,456** bytes (worked out on the head
   dimension page).
3. **Cache at 32,768 tokens:** 147,456 × 32,768 = 4,831,838,208 bytes ≈ **4.5 GiB**.
4. **Cache at 131,072 tokens:** 4 times as many tokens, **18 GiB**.
5. **Scores in one layer at 32,768 tokens:** 32,768² × 32 = **34,359,738,368**.
6. **At 131,072 tokens:** (131,072 ÷ 32,768)² = **16** times as many scores.
7. **For comparison, BERT-base:** learned positions, 512 × 768 = **393,216** parameters, 0.36% of
   its 110,106,428.

**What this means:** going from the native context to the YaRN length multiplies the cache by 4 and
the attention scores by 16, while the parameter count does not change at all.

## What To Watch

- **The card's context, not only config.json.** Use max_position_embeddings as an upper limit.
- **Scaling settings.** A rope_scaling entry (YaRN) changes how far a model reaches; Qwen3-8B's
  config.json ships with none.
- **Memory at your length.** The cache, not the weights, is what grows with long inputs.
