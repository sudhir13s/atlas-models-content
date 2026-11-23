---
id: hyperparameter/precision
type: hyperparameter
slug: precision
schema_version: 2.2
title: "Training Precision"
name: bf16
context: training
priority: 10
importance: important
goals: [training, finetuning]
models: [llm, vision, audio, diffusion, rl]
range: "fp32 | fp16 | bf16 | int8 | 4-bit (NF4)"
controls: "How many bits each number inside the model is stored and computed with during training: fewer bits take less memory, at the cost of how finely and how widely numbers can be written."
effects:
  decrease:
    - "Each stored number takes fewer bytes"
    - "The model, and the batch, take less memory"
    - "Numbers are rounded more coarsely"
    - "With fp16, small gradients can round to zero unless the loss is scaled"
  increase:
    - "Each stored number takes more bytes"
    - "The model takes more memory"
    - "Numbers are written more finely and over a wider span"
    - "At fp32 no loss scaling is needed"
options:
  - value: "fp32"
    effect: "Every number uses 32 bits (4 bytes). The PyTorch default. Widest span and finest steps of the formats here, and the most memory."
  - value: "fp16"
    effect: "Mixed precision with 16-bit floats (2 bytes): 5 exponent bits, 10 significand bits. Largest value 65,504, so the loss must be scaled to keep small gradients from rounding to zero."
  - value: "bf16"
    effect: "Mixed precision with 16-bit brain floats (2 bytes): 8 exponent bits, 7 significand bits. Same span as fp32, coarser steps; Hugging Face notes no loss scaling is required."
  - value: "int8"
    effect: "The frozen base weights are stored as 8-bit integers (1 byte) through bitsandbytes, which halves their memory. Only added parameters, such as LoRA adapters, can be trained."
  - value: "4-bit (NF4)"
    effect: "The frozen base weights are stored in 4 bits (half a byte), the QLoRA method, and computed in bf16. Only added parameters, such as LoRA adapters, can be trained."
typical_values:
  - setting: "PyTorch: default data type of a new tensor"
    value: "fp32"
    source: "https://docs.pytorch.org/docs/2.15/tensors.html"
  - setting: "Hugging Face Transformers: default of TrainingArguments (fp16 = False, bf16 = False)"
    value: "mixed precision off"
    source: "https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py"
  - setting: "Mixed Precision Training (Micikevicius et al.): weights, activations and gradients in half precision, with a single-precision master copy of the weights and loss scaling"
    value: "fp16 + fp32 master copy"
    source: "https://arxiv.org/abs/1710.03740"
  - setting: "Alpaca-LoRA training script: base model loaded in 8 bits with torch_dtype float16, and fp16 = True for training"
    value: "int8 base + fp16"
    source: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py"
  - setting: "QLoRA (Dettmers et al.): base model stored in 4-bit NormalFloat, computed in BFloat16"
    value: "4-bit NF4 base + bf16"
    source: "https://arxiv.org/abs/2305.14314"
mistakes:
  - mistake: "Training in fp16 without loss scaling"
    why: >-
      The PyTorch documentation explains that gradient values with small magnitudes **may not be
      representable in float16**: they **flush to zero** ("underflow"), and the update for those
      weights is lost. The smallest normal fp16 number is about 0.000061 (torch.finfo).
    fix: >-
      In a hand-written PyTorch loop, use gradient scaling with fp16 (`torch.amp.GradScaler`), or
      use bf16, which Hugging Face describes as needing **no loss scaling**.
  - mistake: "Running a model that was trained in bf16 in fp16"
    why: >-
      fp16 cannot hold a number above **65,504**. The PyTorch documentation warns that most
      bf16-pretrained models cannot operate in that range, and that gradients then **overflow**
      instead of underflow.
    fix: >-
      Keep a bf16-trained model in bf16 (or fp32). Hugging Face advises considering BF16 instead of
      FP16 if your hardware supports it.
  - mistake: "Setting both fp16 and bf16 to True"
    why: >-
      They are two choices for the same thing. Hugging Face `TrainingArguments` refuses the
      combination with the error "At most one of fp16 and bf16 can be True, but not both".
    fix: "Pick one 16-bit format and set only that flag."
  - mistake: "Trying to train the 8-bit or 4-bit weights themselves"
    why: >-
      The Hugging Face bitsandbytes documentation states that 8 and 4-bit training is **only
      supported for training extra parameters**. The quantized base weights stay frozen.
    fix: >-
      Add trainable adapters on top, for example LoRA through the PEFT library, as QLoRA does.
  - mistake: "Leaving the 4-bit compute type at its default"
    why: >-
      In bitsandbytes 4-bit loading, the **compute data type defaults to float32**. The Hugging
      Face documentation says to change it to bf16 to speed up computation; QLoRA computes in
      BFloat16.
    fix: "Pass `bnb_4bit_compute_dtype=torch.bfloat16` in `BitsAndBytesConfig`."
tags: [training, precision, mixed-precision, quantization, memory]
updated: 2026-11-23
related: [hyperparameter/lora-r, hyperparameter/target-modules, hyperparameter/gradient-accumulation]
sources:
  - { url: "https://docs.pytorch.org/docs/2.15/tensor_attributes.html", title: "PyTorch documentation: Tensor attributes (torch.dtype bit layouts)", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/2.15/tensors.html", title: "PyTorch documentation: torch.Tensor (default dtype)", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/2.15/type_info.html", title: "PyTorch documentation: torch.finfo", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/2.15/amp.html", title: "PyTorch documentation: Automatic Mixed Precision package (torch.amp)", trust: 0.9 }
  - { url: "https://docs.cloud.google.com/tpu/docs/bfloat16", title: "Google Cloud TPU documentation: The bfloat16 numerical format", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/main_classes/trainer", title: "Hugging Face Transformers documentation: Trainer and TrainingArguments", trust: 0.9 }
  - { url: "https://github.com/huggingface/transformers/blob/main/src/transformers/training_args.py", title: "Hugging Face Transformers source: training_args.py (fp16, bf16 and their check)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/quantization/bitsandbytes", title: "Hugging Face Transformers documentation: bitsandbytes quantization", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1710.03740", title: "Mixed Precision Training (Micikevicius et al., 2017)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2305.14314", title: "QLoRA: Efficient Finetuning of Quantized LLMs (Dettmers et al., 2023)", trust: 0.9 }
  - { url: "https://github.com/tloen/alpaca-lora/blob/main/finetune.py", title: "Alpaca-LoRA training script (finetune.py)", trust: 0.8 }
  - { url: "https://huggingface.co/huggyllama/llama-7b/blob/main/config.json", title: "LLaMA 7B (huggyllama conversion): config.json", trust: 0.8 }
---

## Definition

**Training precision** is the number format used for the numbers inside a model while it trains.

- **Weights** are the numbers a model learns. A large language model has billions of them.
- **Activations** are the in-between results computed as an input flows through the model.
- **Gradients** are the corrections computed for every weight after each batch.
- A **bit** is one binary digit, 0 or 1. Eight bits make one **byte**.
- The **precision** of a format is how many bits each number takes, and how those bits are used.

Fewer bits per number means less memory and, on hardware built for it, faster arithmetic. It also
means numbers are rounded more coarsely and, for some formats, cannot get very large or very small.

**Analogy:** writing prices on a small price tag. A wide tag fits "1,234.56"; a narrow tag forces
you to write "1,230" or to drop the cents. You save space, and you lose detail. The analogy shows
the trade-off only; the exact formats are under How It Works.

In Hugging Face Transformers the choice is made with the switches `fp16` and `bf16` of
`TrainingArguments`, both off by default, and with a `BitsAndBytesConfig` when the base model is
loaded in 8 or 4 bits.

## How It Works

The figure above shows one tile per choice. They fall into two groups.

### Group 1: floating-point formats (fp32, fp16, bf16)

A **floating-point number** is stored like scientific notation: a sign, a **significand** (the
digits) and an **exponent** (where the decimal point goes). PyTorch lists the bit layout of each
format as sign, exponent, significand (S-E-M):

| Format | PyTorch name | Total bits | Sign-exponent-significand bits | Largest value (torch.finfo) |
| --- | --- | --- | --- | --- |
| fp32 | `torch.float32` | 32 | not listed in the table | about 3.4 × 10³⁸ |
| fp16 | `torch.float16` | 16 | 1-5-10 | 65,504 |
| bf16 | `torch.bfloat16` | 16 | 1-8-7 | about 3.4 × 10³⁸ |

How to read the table:

- **More exponent bits give a wider span** of sizes. bf16 has 8, fp16 has 5. The Google Cloud TPU
  documentation states that the dynamic range of bfloat16 and float32 are **equivalent**, and that
  bfloat16 uses half the memory.
- **More significand bits give finer steps** between neighbouring numbers. fp16 has 10, bf16 has
  7, so fp16 rounds more finely and bf16 more coarsely. Part B of the worked example shows the
  steps.

**Mixed precision** uses two formats at once:

- PyTorch's `torch.amp` runs some operations in `torch.float32` and others in a lower-precision
  float, `torch.float16` or `torch.bfloat16`.
- Its documentation explains why: linear layers and convolutions are **much faster** in the lower
  precision, while operations such as reductions (sums over many numbers) often need the
  **dynamic range of float32**.
- Micikevicius et al. keep a **single-precision (fp32) master copy** of the weights that collects
  the updates, rounded to half precision for the computation. With that and loss scaling they
  report nearly halving memory use.

**Loss scaling**, needed with fp16:

1. A gradient smaller than fp16 can hold **rounds to zero**: PyTorch calls this underflow, and the
   update for that weight is lost.
2. The remedy multiplies the loss by a scale factor before the backward pass, so that every
   gradient is larger by the same factor.
3. PyTorch states that each gradient should be **unscaled** (divided by the same factor) before
   the optimizer updates the weights.

Hugging Face describes bf16 as generally preferred over fp16 for better numerical stability and
**no loss scaling required**.

### Group 2: quantized base weights (int8, 4-bit)

**Quantization** stores the frozen base weights in fewer bits, through the bitsandbytes library:

- **int8:** the weights are stored as 8-bit integers. Hugging Face states that quantizing a model
  in 8-bit **halves** its memory use.
- **4-bit:** Hugging Face states that 4-bit quantization reduces memory use by **4 times**.
  QLoRA introduced **4-bit NormalFloat (NF4)**, a 4-bit data type for weights that follow a
  normal (bell-shaped) distribution; Hugging Face says to use NF4 for training 4-bit base models.
- **Computing in 16 bits:** QLoRA keeps one low-precision storage type (usually 4-bit) and one
  computation type (usually BFloat16). Before a matrix multiplication it converts the 4-bit
  weights to BFloat16 and multiplies in 16 bits.
- **Only added weights train.** Hugging Face states that 8 and 4-bit training is only supported
  for extra parameters, such as LoRA adapters.

QLoRA reports that this lets a **65-billion-parameter model** be fine-tuned on a **single 48 GB
GPU** while matching full 16-bit fine-tuning performance.

## Calculation

Two things are calculated: **how much memory the weights take**, and **how fine the steps of a
floating-point format are near 1**.

### 1. Memory for the weights

In words: multiply the number of weights by the bits per weight, and divide by 8 to turn bits
into bytes.

$$
M = \frac{N \times b}{8}
$$

| Symbol | Meaning |
| --- | --- |
| $N$ | the number of weights |
| $b$ | bits per weight: 32 for fp32, 16 for fp16 or bf16, 8 for int8, 4 for 4-bit |
| $M$ | memory in bytes |

Steps:

1. Count the weights $N$.
2. Multiply by the bits per weight $b$.
3. Divide by 8.
4. Divide by 1,000,000,000 to read the result in gigabytes (GB).

This counts the weights only. Training also stores gradients, optimizer state and activations, so
a training run needs more: QLoRA reports that its methods bring the memory needed to fine-tune a
65-billion-parameter model from more than 780 GB down to less than 48 GB.

### 2. The step size near 1

In words: with $m$ significand bits, the gap between 1 and the next larger number the format can
hold is 1 divided by 2, $m$ times. torch.finfo calls this gap `eps`.

$$
\text{eps} = 2^{-m}
$$

| Symbol | Meaning |
| --- | --- |
| $m$ | the number of significand bits (10 for fp16, 7 for bf16) |
| $\text{eps}$ | the gap between 1.0 and the next representable number |

## Worked Example

### Part A: memory for 1 billion weights

A round, made-up model size: $N$ = 1,000,000,000 weights.

1. **fp32:** $1{,}000{,}000{,}000 \times 32 / 8 = 4{,}000{,}000{,}000$ bytes = **4 GB**.
2. **fp16 or bf16:** $1{,}000{,}000{,}000 \times 16 / 8 = 2{,}000{,}000{,}000$ bytes = **2 GB**.
3. **int8:** $1{,}000{,}000{,}000 \times 8 / 8 = 1{,}000{,}000{,}000$ bytes = **1 GB**.
4. **4-bit:** $1{,}000{,}000{,}000 \times 4 / 8 = 500{,}000{,}000$ bytes = **0.5 GB**.

**What this means:** each halving of the bits halves the memory for the weights. The 8-bit and
4-bit figures match the "halves" and "4x" of the Hugging Face documentation, measured from 16 bits.

### Part B: the QLoRA claim

The QLoRA numbers: a 65-billion-parameter model, $N$ = 65,000,000,000, on one 48 GB GPU.

1. **In 16 bits:** $65{,}000{,}000{,}000 \times 16 / 8 = 130{,}000{,}000{,}000$ bytes = **130 GB**.
   That does not fit in 48 GB.
2. **In 4 bits:** $65{,}000{,}000{,}000 \times 4 / 8 = 32{,}500{,}000{,}000$ bytes = **32.5 GB**.
   That fits, with 48 − 32.5 = **15.5 GB** left for the adapters, activations and the rest.

**What this means:** storing the frozen weights in 4 bits is what brings the model under the
memory of one GPU. The constants that 4-bit quantization stores alongside the weights take a
little more; Hugging Face states that nested quantization saves about 0.4 bits per parameter of
that overhead.

### Part C: how fine fp16 and bf16 are near 1

1. **fp16,** $m = 10$: $2^{-10} = 1 / 1{,}024 \approx$ **0.000977**. The next number after 1 is
   about 1.000977.
2. **bf16,** $m = 7$: $2^{-7} = 1 / 128 =$ **0.0078125**. The next number after 1 is 1.0078125.
3. **Compare:** $0.0078125 / 0.000977 = 8$. bf16's steps are 8 times as coarse.

torch.finfo reports the same two values: `eps` = 0.0009765625 for float16 and 0.0078125 for
bfloat16.

**What this means:** adding 0.001 to 1 survives in fp16 but is rounded away in bf16. bf16 trades
fine steps for the wide span of fp32; fp16 trades span for finer steps.

## What To Watch

- **The loss turning into NaN or infinity.** In fp16 a value above 65,504 cannot be stored; the
  PyTorch documentation warns that bf16-pretrained models run in fp16 overflow.
- **Gradients that are exactly zero.** In fp16 without a gradient scaler, small gradients
  underflow to zero (PyTorch).
- **The flags you set.** `fp16` and `bf16` are both `False` by default in Hugging Face
  `TrainingArguments`, and setting both raises an error.
- **The dtype the model was loaded with.** A checkpoint records its own storage type; the
  `config.json` of the LLaMA 7B conversion on the Hugging Face Hub, for example, lists
  `torch_dtype` float16. The bitsandbytes documentation states that `dtype="auto"` loads the model
  in the data type defined in its `config.json`.
- **The 4-bit compute type.** bitsandbytes computes in float32 unless `bnb_4bit_compute_dtype` is
  changed; Hugging Face recommends bf16 for speed.
- **Memory reported after loading.** `model.get_memory_footprint()` in Transformers prints the
  memory the loaded model takes; compare it with Part A.
