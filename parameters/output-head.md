---
id: parameter/output-head
type: parameter
slug: output-head
schema_version: 2.9
title: "Output / Unembedding Head"
name: lm_head
priority: 9
importance: important
applies_to: [llm, transformer, moe]
range: "0 (tied) or 131,072,000 — 926,679,040 parameters"
controls: "The last layer, which turns the model's final vector for a token into one score per token of the vocabulary."
effects:
  decrease:
    - "Fewer parameters, in a straight line with vocab_size and hidden_size"
    - "Fewer multiply-adds per generated token: one per weight"
    - "Tied to the embedding, it adds no parameters but the same multiply-adds"
  increase:
    - "More parameters: 622,329,856 in Qwen3-8B against 131,072,000 in Mistral-7B, at the same hidden_size"
    - "More multiply-adds per generated token"
    - "A larger share of a small model's total"
scale:
  min: 5000000
  max: 3000000000
  typical_min: 131072000
  typical_max: 926679040
  spacing: log
typical_values:
  - setting: "Qwen3-4B, tied to its embedding (config.json)"
    value: "0 extra"
    source: "https://huggingface.co/Qwen/Qwen3-4B/blob/main/config.json"
  - setting: "Mistral-7B-v0.1, 32,000 × 4,096 (config.json)"
    value: "131072000"
    source: "https://huggingface.co/mistralai/Mistral-7B-v0.1/blob/main/config.json"
  - setting: "Qwen3-8B, 151,936 × 4,096 (config.json)"
    value: "622329856"
    source: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json"
  - setting: "Qwen3-32B, 151,936 × 5,120 (config.json)"
    value: "777912320"
    source: "https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json"
  - setting: "DeepSeek-V3, 129,280 × 7,168 (config.json)"
    value: "926679040"
    source: "https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json"
mistakes:
  - mistake: "Thinking the head outputs probabilities"
    why: >-
      It outputs scores, called logits. Transformers' Qwen3 code returns logits = lm_head(hidden
      states); Vaswani et al. apply a softmax after this linear layer to get next-token probabilities.
    fix: "Apply a softmax (and any sampling settings) to the logits to get probabilities."
  - mistake: "Assuming a tied head costs nothing to run"
    why: >-
      Tying saves parameters only. The code runs lm_head on the final vectors either way, one
      multiply-add per weight: **622,329,856** per token in Qwen3-8B.
    fix: "Count the head's computation from vocab_size × hidden_size whether or not it is tied."
  - mistake: "Expecting the head to grow with the model's depth"
    why: >-
      It is set by vocab_size and hidden_size alone. Mistral-7B and Qwen3-8B share hidden_size
      **4096**, yet their heads differ about **4.7** times, the ratio of their vocabularies.
    fix: "Read vocab_size and hidden_size to size the head."
related: [parameter/tied-embeddings, parameter/token-embedding, parameter/vocab-size, parameter/hidden-size]
visual:
  kind: count_breakdown
  models:
    - { name: "Mistral-7B-v0.1", vocab_size: 32000, hidden_size: 4096, intermediate_size: 14336, num_hidden_layers: 32, num_attention_heads: 32, num_key_value_heads: 8, head_dim: 128, tie_word_embeddings: false, ffn_matrices: 3, qk_norm: false }
    - { name: "Qwen3-8B", vocab_size: 151936, hidden_size: 4096, intermediate_size: 12288, num_hidden_layers: 36, num_attention_heads: 32, num_key_value_heads: 8, head_dim: 128, tie_word_embeddings: false, ffn_matrices: 3, qk_norm: true }
  caption: "Two untied models with the same hidden_size, 4096, computed from their config.json files: Mistral-7B's head is 1.8% of its parameters, Qwen3-8B's 7.6%, because its vocabulary is 4.7 times larger. Mistral-7B sets no head_dim; Transformers uses 4096 ÷ 32 = 128. Both totals match the counts on their catalog pages."
tags: [llm, transformer, output, lm-head, unembedding, parameters]
updated: 2026-11-23
sources:
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py", title: "Hugging Face Transformers source: modeling_qwen3.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/mistral/configuration_mistral.py", title: "Hugging Face Transformers source: configuration_mistral.py", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1608.05859", title: "Using the Output Embedding to Improve Language Models (Press & Wolf, 2017)", trust: 0.85 }
  - { url: "https://huggingface.co/mistralai/Mistral-7B-v0.1/blob/main/config.json", title: "Mistral-7B-v0.1 configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json", title: "Qwen3-8B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-4B/blob/main/config.json", title: "Qwen3-4B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-32B/blob/main/config.json", title: "Qwen3-32B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/deepseek-ai/DeepSeek-V3/blob/main/config.json", title: "DeepSeek-V3 configuration (config.json)", trust: 0.95 }
---

## Definition

**The output head turns the model's final vector for a token into one score for every token in the
vocabulary.** In Transformers it is the module lm_head, a linear layer from hidden_size to
vocab_size with no bias; it is a module name, not a configuration key.

- **A score per token:** these raw scores are the **logits**; a softmax turns them into
  next-token probabilities.
- **Its shape** mirrors the token embedding: vocab_size × hidden_size. Press and Wolf call it the
  output embedding.
- **Tied or not:** it can reuse the embedding matrix; see the tied embeddings page.
- **The figure** shows its share: **1.8%** of Mistral-7B, **7.6%** of Qwen3-8B.

**Analogy:** a scoreboard with one light per word, lit by how well each word fits next. The analogy
shows the idea only; the exact rule is under Calculation.

## How It Works

**Final vector in, one score per token out.**

1. **Normalise.** In Qwen3 the last layer's output passes a final RMSNorm.
2. **Score.** lm_head multiplies the vector by every row of its matrix: one dot product per token of
   the vocabulary.
3. **Choose.** A softmax over the logits gives probabilities, from which the next token is picked.
4. **Repeat.** While generating, this runs once for every new token.

## Calculation

**Parameters and work of the head.**

In words: one weight per vocabulary token per hidden number; each weight is used once per token
scored.

$$
\text{head} = V \times d \quad (0 \text{ extra if tied}) \qquad \text{multiply-adds per token} = V \times d
$$

| Symbol | Meaning |
| --- | --- |
| $V$ | vocab_size |
| $d$ | hidden_size |

For whole-model counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Qwen3-8B and Mistral-7B-v0.1, from their config.json files** (the numbers on their catalog
pages): both hidden_size **4096** and untied; vocab_size **151,936** and **32,000**.

1. **Qwen3-8B's head:** 151,936 × 4,096 = **622,329,856** parameters.
2. **Its output:** **151,936** logits for each token, from 622,329,856 multiply-adds.
3. **Its share:** 622,329,856 ÷ 8,190,735,360 ≈ **7.6%**.
4. **Mistral-7B's head:** 32,000 × 4,096 = **131,072,000**, ≈ **1.8%** of its 7,241,732,096.
5. **Ratio:** 622,329,856 ÷ 131,072,000 ≈ **4.7**, the same as 151,936 ÷ 32,000.

**What this means:** at the same width, the head's size follows the vocabulary directly.

## What To Watch

- **tie_word_embeddings.** Tied, the head adds no parameters but still costs its computation.
- **vocab_size.** It sets the number of logits and the head's size.
- **Logits, not probabilities.** Sampling settings act on the logits before or during the softmax.
