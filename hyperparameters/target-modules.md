---
id: hyperparameter/target-modules
type: hyperparameter
slug: target-modules
schema_version: 2.2
title: "LoRA Target Modules"
name: target_modules
context: training
priority: 7
importance: important
goals: [finetuning]
models: [llm, vision, audio, diffusion]
range: "query and value matrices only — all linear layers"
controls: "Which of a model's weight matrices get a LoRA adapter: every matrix not listed stays exactly as it was."
effects:
  decrease:
    - "Fewer matrices get an adapter"
    - "Fewer trainable numbers and a smaller adapter file"
    - "Fewer places where the model can change"
  increase:
    - "More matrices get an adapter"
    - "More trainable numbers, in proportion to the matrices added"
    - "QLoRA: adapters on all linear layers were needed to match full fine-tuning"
options:
  - value: "None (the default)"
    effect: "PEFT picks a list for the model's architecture: for LLaMA, Mistral and Qwen2, q_proj and v_proj. For an architecture it does not know, it raises an error."
  - value: "[\"q_proj\", \"v_proj\"]"
    effect: "Adapters on the attention query and value matrices only, as in the LoRA paper's main experiments. The fewest trainable numbers of these choices."
  - value: "[\"q_proj\", \"k_proj\", \"v_proj\", \"o_proj\"]"
    effect: "Adapters on all four attention matrices. The LoRA paper's Table 5 tested this at the same budget as one or two matrices."
  - value: "\"all-linear\""
    effect: "Adapters on every linear layer, attention and feed-forward, except the output layer. The QLoRA setting; the most trainable numbers of these choices."
typical_values:
  - setting: "Hugging Face PEFT: default when target_modules is not set, for LLaMA, Mistral, Qwen2, Gemma and OPT"
    value: "q_proj, v_proj"
    source: "https://github.com/huggingface/peft/blob/main/src/peft/utils/constants.py"
  - setting: "Hugging Face PEFT: default when target_modules is not set, for BERT and RoBERTa (GPT-2: c_attn; T5: q, v)"
    value: "query, value"
    source: "https://github.com/huggingface/peft/blob/main/src/peft/utils/constants.py"
  - setting: "LoRA paper: RoBERTa, DeBERTa, GPT-2 and GPT-3 experiments"
    value: "Wq, Wv (attention query and value)"
    source: "https://arxiv.org/abs/2106.09685"
  - setting: "Alpaca-LoRA training script: default"
    value: "q_proj, v_proj"
    source: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py"
  - setting: "QLoRA: LLaMA fine-tuning (the training script collects every linear layer and drops lm_head)"
    value: "all linear layers"
    source: "https://arxiv.org/abs/2305.14314"
mistakes:
  - mistake: "Assuming LoRA adapts every layer when target_modules is left out"
    why: >-
      The PEFT documentation states that the **default settings add trainable weights to the query
      and value layers** of each attention block; for LLaMA the default list is `q_proj` and
      `v_proj`. QLoRA reports that LoRA on **all linear transformer block layers is required to
      match full fine-tuning** performance.
    fix: >-
      Set `target_modules="all-linear"` when you want QLoRA's setting; PEFT documents it as easier
      than naming the modules one by one.
  - mistake: "Copying module names from a model of another architecture"
    why: >-
      Names differ between architectures. PEFT's own defaults use `q_proj`/`v_proj` for LLaMA,
      `query`/`value` for BERT, `c_attn` for GPT-2, `q`/`v` for T5 and `query_key_value` for
      Falcon. The PEFT documentation notes that names **vary by architecture**.
    fix: >-
      Read the names from the model itself (for example with `model.named_modules()` in PyTorch),
      or use `"all-linear"`, which needs no names.
  - mistake: "Expecting a name in the list to match one layer only"
    why: >-
      PEFT matches a listed name **as a suffix**: the PEFT quicktour notes that `["q_proj"]` targets
      the `q_proj` matrix of **every** layer of the model.
    fix: >-
      To target only some layers, pass a single string, which PEFT reads as a regular expression
      (a text pattern), or list the layers to skip in `exclude_modules`.
  - mistake: "Raising the rank instead of adapting more matrices"
    why: >-
      At the same budget of 18 million parameters on GPT-3, the LoRA paper's Table 5 gives **70.4**
      on WikiSQL for Wq alone at r = 8, **73.7** for Wq and Wv at r = 4, and **73.7** for all four
      attention matrices at r = 2. QLoRA calls the total number of adapters the most critical
      LoRA hyperparameter.
    fix: >-
      Spread a fixed budget over more matrices before raising the rank; see the LoRA rank page.
tags: [finetuning, lora, peft, architecture]
updated: 2026-11-23
related: [hyperparameter/lora-r, hyperparameter/lora-alpha, hyperparameter/precision]
sources:
  - { url: "https://arxiv.org/abs/2106.09685", title: "LoRA: Low-Rank Adaptation of Large Language Models (Hu et al., 2021)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2305.14314", title: "QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., 2017)", trust: 0.9 }
  - { url: "https://github.com/huggingface/peft/blob/main/src/peft/tuners/lora/config.py", title: "Hugging Face PEFT source: LoraConfig (target_modules, exclude_modules)", trust: 0.9 }
  - { url: "https://github.com/huggingface/peft/blob/main/src/peft/utils/constants.py", title: "Hugging Face PEFT source: constants.py (default target modules per architecture)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/peft/developer_guides/lora", title: "Hugging Face PEFT documentation: LoRA", trust: 0.9 }
  - { url: "https://huggingface.co/docs/peft/quicktour", title: "Hugging Face PEFT documentation: Quicktour", trust: 0.9 }
  - { url: "https://github.com/artidoro/qlora/blob/main/qlora.py", title: "QLoRA training script (qlora.py)", trust: 0.85 }
  - { url: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py", title: "Alpaca-LoRA training script (finetune.py)", trust: 0.8 }
  - { url: "https://huggingface.co/huggyllama/llama-7b/blob/main/config.json", title: "LLaMA 7B (huggyllama conversion): config.json", trust: 0.8 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/modeling_llama.py", title: "Hugging Face Transformers source: modeling_llama.py (layer shapes)", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/models/llama/configuration_llama.py", title: "Hugging Face Transformers source: configuration_llama.py", trust: 0.9 }
---

## Definition

**Target modules** are the weight matrices of a model that Low-Rank Adaptation (LoRA) attaches an
adapter to.

- A **weight matrix** is a table of numbers the model learned. A **linear layer** (a "module" in
  PyTorch's terms) multiplies its input by one such matrix.
- **LoRA** fine-tunes a model by keeping these matrices **frozen** and training a small
  **adapter** next to chosen ones: two thin matrices whose product is a change to add.
- `target_modules` is the list of **which** linear layers get an adapter. Every other layer stays
  exactly as it was.

In Hugging Face PEFT the setting is the `target_modules` field of `LoraConfig`. Its default is
`None`, which tells PEFT to pick a list for the model's architecture.

**Analogy:** renovating a house by choosing which rooms to work on. Working on two rooms is cheap;
working on every room costs more and allows more change. The analogy shows the choice only; the
exact counts are under Calculation.

## How It Works

The tiles above show the four common choices, from fewest adapted matrices to most.

### The linear layers of a transformer block

A transformer language model is a stack of identical **blocks** (layers). In LLaMA, as written in
Hugging Face Transformers, each block has seven linear layers in two groups:

| Group | Layer | What it does |
| --- | --- | --- |
| Attention | `q_proj` | turns the input into **queries** |
| Attention | `k_proj` | turns the input into **keys** |
| Attention | `v_proj` | turns the input into **values** |
| Attention | `o_proj` | the **output** projection: projects the combined attention result back |
| Feed-forward (MLP) | `gate_proj`, `up_proj` | each maps a vector of `hidden_size` numbers to `intermediate_size` numbers |
| Feed-forward (MLP) | `down_proj` | maps `intermediate_size` numbers back to `hidden_size` |

What those words mean, as the Transformer paper (Vaswani et al.) defines them:

- **Attention** maps a query and a set of key-value pairs to an output: a weighted sum of the
  values, where each value's weight comes from how well the query matches that value's key.
- The queries, keys and values are made by **learned linear projections** of the input; after
  attention the results are joined and **projected once again** (the output projection).
- The **feed-forward network**, also called the MLP (multi-layer perceptron), is applied to
  **each position** (each token of the text) separately and identically.

### What the papers adapted

- **LoRA paper (Hu et al.):** the authors limited their study to **adapting only the attention
  weights** and froze the MLP modules. Their main experiments adapt the query and value matrices
  (Wq and Wv).
- **LoRA paper, Table 5:** with the budget fixed at 18 million parameters on GPT-3 175B, they
  compared which attention matrices to adapt:

| Matrices adapted | Rank | WikiSQL | MultiNLI |
| --- | --- | --- | --- |
| Wq | 8 | 70.4 | 91.0 |
| Wk | 8 | 70.0 | 90.8 |
| Wv | 8 | 73.0 | 91.0 |
| Wo | 8 | 73.2 | 91.3 |
| Wq, Wk | 4 | 71.4 | 91.3 |
| Wq, Wv | 4 | 73.7 | 91.3 |
| Wq, Wk, Wv, Wo | 2 | 73.7 | 91.7 |

  At the same budget, **more matrices at a lower rank** scored as high as or higher than one
  matrix at a higher rank.
- **QLoRA (Dettmers et al.):** reports that the **most critical LoRA hyperparameter is how many
  adapters are used in total**, and that LoRA on **all linear transformer block layers** is
  required to match full fine-tuning. It used adapters on all linear layers of LLaMA.

### How PEFT reads the setting

| What you pass | What PEFT does (`LoraConfig` documentation) |
| --- | --- |
| nothing (`None`) | picks the architecture's list from `constants.py`; for an unknown architecture it raises an error |
| a list of names, e.g. `["q_proj", "v_proj"]` | matches the names as suffixes, so each name is adapted in every layer (PEFT quicktour) |
| one string other than `"all-linear"` | reads it as a regular expression (a text pattern for module names) |
| `"all-linear"` | adapts all linear (and Conv1D) layers, leaving out the output layer of a `PreTrainedModel` |

The `LoraConfig` documentation gives `'.*decoder.*(SelfAttention|EncDecAttention).*(q|v)$'` as
an example pattern. `exclude_modules` takes names or a pattern of modules to leave out.

## Calculation

One thing is calculated: **how many trainable numbers a choice of target modules adds**.

### 1. One matrix

In words: an adapter on a matrix with $k$ inputs and $d$ outputs holds $r$ times $(d + k)$ numbers
(the LoRA rank page explains why).

$$
P = r\,(d + k)
$$

### 2. The whole model

In words: add $P$ over every targeted matrix of one block, then multiply by the number of blocks.

$$
P_\text{total} = L \times \sum_{m \,\in\, \text{targets}} r\,(d_m + k_m)
$$

| Symbol | Meaning |
| --- | --- |
| $r$ | the LoRA rank |
| $d_m$, $k_m$ | output and input size of targeted matrix $m$ |
| $\sum$ | "add up over": here, over every targeted matrix of one block |
| $L$ | the number of blocks (layers) |
| $P_\text{total}$ | trainable numbers LoRA adds |

Steps:

1. Read `hidden_size`, `intermediate_size`, the head counts and `num_hidden_layers` from the
   model's `config.json`.
2. Work out each targeted layer's input and output size.
3. Compute $r\,(d + k)$ for each targeted layer and add them for one block.
4. Multiply by the number of blocks.

## Worked Example

### Part A: the layer sizes of LLaMA 7B

The numbers come from the `config.json` of the LLaMA 7B conversion on the Hugging Face Hub:
`hidden_size` **4,096**, `intermediate_size` **11,008**, **32** attention heads, **32** layers.
The config gives no `num_key_value_heads`, and Transformers then uses the number of attention
heads.

1. Head size: $4{,}096 / 32 = 128$.
2. `q_proj`, `k_proj`, `v_proj`, `o_proj`: $32 \times 128 = 4{,}096$, so each is 4,096 in and
   4,096 out. $d + k = 4{,}096 + 4{,}096 = 8{,}192$.
3. `gate_proj`, `up_proj`: 4,096 in, 11,008 out. `down_proj`: 11,008 in, 4,096 out. Each has
   $d + k = 4{,}096 + 11{,}008 = 15{,}104$.

### Part B: three choices at the PEFT default rank r = 8

1. **q_proj and v_proj** (PEFT's LLaMA default):
   per block $2 \times 8 \times 8{,}192 = 131{,}072$;
   total $32 \times 131{,}072 =$ **4,194,304**.
2. **All four attention layers:**
   per block $4 \times 8 \times 8{,}192 = 262{,}144$;
   total $32 \times 262{,}144 =$ **8,388,608**.
3. **"all-linear"** (attention and feed-forward):
   feed-forward per block $3 \times 8 \times 15{,}104 = 362{,}496$;
   per block $262{,}144 + 362{,}496 = 624{,}640$;
   total $32 \times 624{,}640 =$ **19,988,480**.

### Part C: against the frozen matrices

1. Frozen numbers in one block: attention $4 \times 4{,}096 \times 4{,}096 = 67{,}108{,}864$;
   feed-forward $3 \times 4{,}096 \times 11{,}008 = 135{,}266{,}304$; sum $202{,}375{,}168$.
2. In 32 blocks: $32 \times 202{,}375{,}168 = 6{,}476{,}005{,}376$ (this leaves out the word
   embeddings and the output layer).
3. Shares: $4{,}194{,}304 / 6{,}476{,}005{,}376 \approx 0.065\%$ for q and v;
   $19{,}988{,}480 / 6{,}476{,}005{,}376 \approx 0.31\%$ for all-linear.

**What this means:** "all-linear" trains about **4.8 times** as many numbers as the default
(19,988,480 / 4,194,304 ≈ 4.77), and still well under 1% of the frozen matrices.

## What To Watch

- **The modules PEFT actually wrapped.** PEFT's `print_healthcheck()` prints the number of adapter
  layers and the trainable parameters. With q_proj and v_proj targeted, expect 2 adapter layers
  per block: 64 for the 32 blocks of LLaMA 7B.
- **The trainable count.** Compare it with Part B; a count far from the formula means the names
  matched more or fewer modules than intended.
- **The rank and alpha.** Adding matrices adds trainable numbers in proportion; the rank and alpha
  pages cover the other two dials.
- **The base model's precision.** QLoRA put adapters on all linear layers of a model stored in 4
  bits; see the training precision page.
