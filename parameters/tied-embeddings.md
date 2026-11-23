---
id: parameter/tied-embeddings
type: parameter
slug: tied-embeddings
schema_version: 2.9
title: "Tied vs Untied Embeddings"
name: tie_word_embeddings
priority: 14
importance: optional
applies_to: [llm, transformer, moe]
range: "true · false"
controls: "Whether the output head reuses the token embedding matrix instead of having its own; tying saves one vocab_size × hidden_size matrix."
options:
  - value: "true"
    effect: "The output head is the embedding matrix itself: vocab_size × hidden_size fewer parameters, 388,956,160 in Qwen3-4B."
  - value: "false"
    effect: "The output head is a separate matrix of vocab_size × hidden_size: 622,329,856 in Qwen3-8B, 7.6% of its total."
typical_values:
  - setting: "Original Transformer: one matrix for both embeddings and the pre-softmax layer (Vaswani et al.)"
    value: "shared"
    source: "https://arxiv.org/abs/1706.03762"
  - setting: "SmolLM2-135M (config.json)"
    value: "true"
    source: "https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json"
  - setting: "Qwen3-0.6B, 1.7B and 4B (Qwen3 report, Table 1)"
    value: "true"
    source: "https://arxiv.org/abs/2505.09388"
  - setting: "Qwen3-4B (config.json)"
    value: "true"
    source: "https://huggingface.co/Qwen/Qwen3-4B/blob/main/config.json"
  - setting: "Qwen3-8B, 14B and 32B (Qwen3 report, Table 1)"
    value: "false"
    source: "https://arxiv.org/abs/2505.09388"
  - setting: "Mixtral-8x7B (config.json)"
    value: "false"
    source: "https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json"
mistakes:
  - mistake: "Thinking tying removes the embedding table"
    why: >-
      It removes the second copy. Transformers' Qwen3 code ties lm_head.weight to
      model.embed_tokens.weight, so one matrix serves both. Qwen3-4B's published **4.0B** counts the
      table once: **4,022,468,096**, against **4,411,424,256** if the head were separate.
    fix: "Count a tied table once and the output head as 0, as in the worked example."
  - mistake: "Assuming a configuration without the key is untied"
    why: >-
      The default differs by model class in Transformers: **True** for BertConfig, **False** for
      MixtralConfig and Qwen3MoeConfig.
    fix: "Read the key in config.json; if it is missing, read the model class's default."
  - mistake: "Treating tying as a size saving only"
    why: >-
      Press and Wolf, who recommend tying, report better perplexity with it: their large language
      model went from **66M** to **51M** parameters and its test perplexity from **78.4** to
      **74.3** (lower is better). In translation, tied models with about **28%** fewer parameters
      performed about the same.
    fix: "Judge tying by a model's published results, not only by its parameter count."
related: [parameter/token-embedding, parameter/output-head, parameter/vocab-size, parameter/hidden-size]
visual:
  kind: count_breakdown
  models:
    - { name: "Qwen3-4B", vocab_size: 151936, hidden_size: 2560, intermediate_size: 9728, num_hidden_layers: 36, num_attention_heads: 32, num_key_value_heads: 8, head_dim: 128, tie_word_embeddings: true, ffn_matrices: 3, qk_norm: true }
    - { name: "Qwen3-8B", vocab_size: 151936, hidden_size: 4096, intermediate_size: 12288, num_hidden_layers: 36, num_attention_heads: 32, num_key_value_heads: 8, head_dim: 128, tie_word_embeddings: false, ffn_matrices: 3, qk_norm: true }
  caption: "Two neighbouring Qwen3 models, computed from their config.json files: Qwen3-4B ties its output head to the embedding, so the head adds nothing; Qwen3-8B does not, so its head adds another 7.6%. Both totals match the published counts."
tags: [llm, transformer, weight-tying, embedding, output, parameters]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/1608.05859", title: "Using the Output Embedding to Improve Language Models (Press & Wolf, 2017)", trust: 0.85 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2505.09388", title: "Qwen3 Technical Report (Qwen Team, 2025)", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen3-4B/blob/main/config.json", title: "Qwen3-4B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/Qwen/Qwen3-4B", title: "Qwen3-4B model card", trust: 0.9 }
  - { url: "https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json", title: "Qwen3-8B configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/HuggingFaceTB/SmolLM2-135M/blob/main/config.json", title: "SmolLM2-135M configuration (config.json)", trust: 0.95 }
  - { url: "https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1/blob/main/config.json", title: "Mixtral-8x7B-Instruct-v0.1 configuration (config.json)", trust: 0.95 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3/modeling_qwen3.py", title: "Hugging Face Transformers source: modeling_qwen3.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/utils/auto_docstring.py", title: "Hugging Face Transformers source: configuration argument descriptions (auto_docstring.py)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/bert/configuration_bert.py", title: "Hugging Face Transformers source: configuration_bert.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/mixtral/configuration_mixtral.py", title: "Hugging Face Transformers source: configuration_mixtral.py", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen3_moe/configuration_qwen3_moe.py", title: "Hugging Face Transformers source: configuration_qwen3_moe.py", trust: 0.9 }
---

## Definition

**tie_word_embeddings says whether the output head reuses the token embedding matrix.**
Transformers describes the key as whether to tie weight embeddings according to the model's
tied-weights mapping.

- **The token embedding** turns each token number into a vector of hidden_size numbers: one row per
  token, vocab_size rows.
- **The output head** does the reverse at the end: it scores every token in the vocabulary from the
  last vector, with a matrix of the same shape.
- **Tied (true):** one matrix does both jobs. **Untied (false):** two separate matrices.
- **The figure** shows the cost: nothing extra in Qwen3-4B, **7.6%** extra in Qwen3-8B.

**Analogy:** one dictionary used both to look words up and to check spellings, instead of buying a
second copy. The analogy shows the idea only; the exact counts are under Calculation.

## How It Works

**One matrix, read two ways.**

1. **In.** Each token number picks its row of the embedding matrix.
2. **Through.** The layers turn those vectors into a final vector per token.
3. **Out.** The output head multiplies the final vector by every row, giving one score per token.
   Tied, these rows are the embedding rows; Press and Wolf call them the input and output
   embeddings.
4. **Training.** With tying, both uses update the same numbers; Press and Wolf find that the tied
   matrix evolves more like an output embedding than an input one.

**Who ties:** Vaswani et al. shared one matrix between both embedding layers and the pre-softmax
layer. In the Qwen3 report, the 0.6B, 1.7B and 4B models tie; the 8B, 14B and 32B do not.

## Calculation

**What the output head adds to the count.**

In words: untied, the head is a second vocab_size × hidden_size matrix; tied, it adds nothing.

$$
\text{output head} = \begin{cases} 0 & \text{tied} \\ V \times d & \text{untied} \end{cases}
$$

| Symbol | Meaning |
| --- | --- |
| $V$ | vocab_size |
| $d$ | hidden_size |

The embedding table itself is always $V \times d$. For whole-model counts, see the
[parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**Qwen3-4B and Qwen3-8B, from their config.json files** (the numbers on their catalog pages): both
have vocab_size **151,936**; Qwen3-4B has hidden_size **2560** and ties, Qwen3-8B has **4096** and
does not.

1. **Qwen3-4B's table:** 151,936 × 2,560 = **388,956,160**.
2. **Its head:** tied, so **0** more. Total: **4,022,468,096**, the published 4.0B.
3. **If it were untied:** 4,022,468,096 + 388,956,160 = **4,411,424,256**, 9.7% more.
4. **Qwen3-8B's head:** 151,936 × 4,096 = **622,329,856**, its own matrix.
5. **Share of Qwen3-8B:** 622,329,856 ÷ 8,190,735,360 ≈ **7.6%**; tied, the model would have
   **7,568,405,504**.

**What this means:** tying saves exactly one vocab_size × hidden_size matrix; Qwen3 does it in its
three smallest models.

## What To Watch

- **The key in config.json.** Its default differs between model classes.
- **Published counts.** A tied model's total counts the shared table once.
- **The run-time cost.** Tying saves parameters, not the output head's computation; see the output
  head page.
