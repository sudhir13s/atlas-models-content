---
id: quantization-topic/number-formats
type: quantization-topic
slug: number-formats
schema_version: 2.6
title: "Number Formats and What Quantization Is"
order: 1
summary: "How a model's weights are stored as numbers, and how quantization stores each weight in fewer bits by mapping it onto a few whole numbers."
visuals:
  - kind: bit_layout
    section: "How A Number Is Stored"
    formats:
      - name: "FP32"
        fields: [{ role: sign, bits: 1 }, { role: exponent, bits: 8 }, { role: mantissa, bits: 23 }]
      - name: "FP16"
        fields: [{ role: sign, bits: 1 }, { role: exponent, bits: 5 }, { role: mantissa, bits: 10 }]
      - name: "BF16"
        fields: [{ role: sign, bits: 1 }, { role: exponent, bits: 8 }, { role: mantissa, bits: 7 }]
      - name: "INT8"
        fields: [{ role: integer, bits: 8 }]
      - name: "4-bit"
        fields: [{ role: integer, bits: 4 }]
    caption: "One weight in each format, one square per bit. A row's length is the memory one weight takes."
  - kind: affine_example
    section: "How Quantization Works"
    bits: 4
    weights: [0.23, -0.6, 0.46, 0.9]
    caption: "Four example weights, not from a trained model, quantized to 4 bits (the whole numbers 0 to 15) with one scale and zero point, then turned back into decimals."
  - kind: model_memory
    section: "Memory At Each Precision"
    model: model/llama-3-1-8b-instruct
    precisions:
      - { label: "32-bit", bits: 32 }
      - { label: "16-bit", bits: 16 }
      - { label: "8-bit", bits: 8 }
      - { label: "4-bit", bits: 4 }
    caption: "The weights of Llama 3.1 8B Instruct at 32 bits (FP32), 16 bits (FP16 or BF16), 8 bits (INT8) and 4 bits: its parameter count × bits per weight ÷ 8, in gigabytes (1 GB = 1,000,000,000 bytes). Weights only."
tables:
  - section: "The Formats Side By Side"
    title: "The formats side by side"
    columns: ["Format", "Total bits", "How the bits are split", "Bytes per weight (bits ÷ 8)"]
    rows:
      - { cells: ["FP32 (single precision)", "32", "1 sign, 8 exponent, 23 mantissa", "4"], source: "https://www.intel.com/content/dam/develop/external/us/en/documents/bf16-hardware-numerics-definition-white-paper.pdf" }
      - { cells: ["FP16 (half precision)", "16", "1 sign, 5 exponent, 10 mantissa", "2"], source: "https://docs.nvidia.com/deeplearning/performance/mixed-precision-training/index.html" }
      - { cells: ["BF16 (bfloat16)", "16", "1 sign, 8 exponent, 7 mantissa", "2"], source: "https://cloud.google.com/blog/products/ai-machine-learning/bfloat16-the-secret-to-high-performance-on-cloud-tpus" }
      - { cells: ["INT8", "8", "a whole number from −128 to 127", "1"], source: "https://huggingface.co/docs/transformers/quantization/concept_guide" }
      - { cells: ["4-bit integer", "4", "one of 16 whole numbers", "0.5"], source: "https://huggingface.co/docs/transformers/quantization/concept_guide" }
  - section: "What Is Lost"
    title: "Perplexity increase per llama.cpp file type"
    columns: ["File type", "Perplexity increase on Llama-3-8B"]
    rows:
      - { cells: ["Q8_0", "+0.0026"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
      - { cells: ["Q6_K", "+0.0217"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
      - { cells: ["Q5_K_M", "+0.0569"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
      - { cells: ["Q5_K_S", "+0.1049"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
      - { cells: ["Q4_K_M", "+0.1754"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
      - { cells: ["Q4_K_S", "+0.2689"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
      - { cells: ["Q4_0", "+0.4685"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
      - { cells: ["Q3_K_M", "+0.6569"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
      - { cells: ["Q3_K_S", "+1.6321"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
      - { cells: ["Q2_K", "+3.5199"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp" }
tags: [quantization, precision, number-formats, memory]
updated: 2026-11-23
related: [quantization-topic/reading-quantized-file-names, hyperparameter/precision, model/llama-3-1-8b-instruct]
sources:
  - { url: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md", title: "llama.cpp quantize README: tools/quantize/README.md (llama-quantize)", trust: 0.95 }
  - { url: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp", title: "llama.cpp quantize.cpp: the list of file types (tools/quantize/quantize.cpp)", trust: 0.95 }
  - { url: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h", title: "llama.cpp ggml-common.h: quantized block layouts (ggml/src/ggml-common.h)", trust: 0.95 }
  - { url: "https://github.com/ggml-org/llama.cpp/blob/master/src/llama-quant.cpp", title: "llama.cpp llama-quant.cpp: which tensors each file type keeps at more bits (src/llama-quant.cpp)", trust: 0.95 }
  - { url: "https://huggingface.co/docs/hub/gguf", title: "Hugging Face Hub GGUF documentation (quantization types)", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/quantization/concept_guide", title: "Hugging Face Transformers quantization concepts", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/quantization/bitsandbytes", title: "Hugging Face Transformers bitsandbytes documentation", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/perplexity", title: "Hugging Face Transformers perplexity documentation (Perplexity of fixed-length models)", trust: 0.9 }
  - { url: "https://www.intel.com/content/dam/develop/external/us/en/documents/bf16-hardware-numerics-definition-white-paper.pdf", title: "Intel: BFLOAT16 – Hardware Numerics Definition (white paper, 2018)", trust: 0.95 }
  - { url: "https://cloud.google.com/blog/products/ai-machine-learning/bfloat16-the-secret-to-high-performance-on-cloud-tpus", title: "Google Cloud blog: BFloat16, the secret to high performance on Cloud TPUs", trust: 0.9 }
  - { url: "https://docs.nvidia.com/deeplearning/performance/mixed-precision-training/index.html", title: "NVIDIA documentation: Train With Mixed Precision", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/2.15/tensor_attributes.html", title: "PyTorch documentation: Tensor attributes (torch.dtype bit layouts)", trust: 0.9 }
  - { url: "https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct", title: "Meta Llama 3.1 8B Instruct model card (Hugging Face)", trust: 0.9 }
---

## What It Is

**Quantization stores each of a model's numbers in fewer bits.**

- **Weights** are the numbers a model learned in training. Llama 3.1 8B Instruct has **8,030,261,248** of them (its record in this library, read from its Hugging Face card).
- A **bit** is one binary digit, 0 or 1. **Eight bits make one byte.**
- Weights are stored as **decimal (floating-point) numbers**; the llama.cpp README notes that models are usually distributed in **16-bit** format. Quantization rewrites each weight as a **small whole number** of 8 bits, 4 bits or fewer, plus a few shared numbers that turn it back into an approximate weight.
- **What it is for:** the llama.cpp documentation says quantization reduces the precision of the weights, which **shrinks the model's size** and can speed up inference.
- **What it costs:** the same documentation says it may introduce some **accuracy loss**, usually measured in perplexity (see What Is Lost below).

## How A Number Is Stored

**Each row of the figure is one weight; each square is one bit.** The colour says what the bit does.

**Floating-point formats (FP32, FP16, BF16)** store a number the way scientific notation does, in three parts:

- **Sign** (1 bit): plus or minus.
- **Exponent:** how large the number is, as a power of 2. More exponent bits give a **wider range**, from very small to very large numbers.
- **Mantissa:** the digits of the number. More mantissa bits give **finer steps** between neighbouring numbers. NVIDIA calls these the fraction bits.

The three floating-point formats:

- **FP32** (single precision): **1 sign, 8 exponent and 23 mantissa bits**, 32 in all.
  - Intel describes BF16 as a short version of FP32 that **skips the 16 least significant mantissa bits**, and Google states that BF16 has the **same exponent size as FP32**.
  - So FP32 has the same 1 sign and 8 exponent bits, and 7 + 16 = **23** mantissa bits.
- **FP16** (half precision): **1 sign, 5 exponent and 10 mantissa bits**, the layout NVIDIA quotes from the IEEE 754 standard. NVIDIA gives **65,504** as the largest value FP16 can hold.
- **BF16** (bfloat16, which PyTorch calls "brain floating point"): **1 sign, 8 exponent and 7 mantissa bits** (Google Cloud). Google states its dynamic range is **identical to FP32's**.

**Integer formats (INT8, 4-bit)** have no exponent and no mantissa: each value is a **whole number**.

- **INT8** holds the **256 whole numbers from −128 to 127** (Hugging Face).
- A **4-bit integer** holds **16 values** (Hugging Face); in the figure on How Quantization Works they are 0 to 15.
- Hugging Face notes that most hardware has no 4-bit storage type, so **two 4-bit values are packed into one byte**.

## The Formats Side By Side

**The same five formats, with the source of each layout.**

- **Bytes per weight** is the total bits divided by 8: one FP32 weight takes **4 bytes**, one 4-bit weight **half a byte**.
- **A quantized file is a little larger than this column says**, because it also stores the shared numbers that turn the whole numbers back into weights (How Quantization Works, below).

## How Quantization Works

**Quantization maps a range of decimal numbers onto a few evenly spaced whole numbers.** Hugging Face calls the most common mapping **affine quantization**.

**How the mapping is set** (the Hugging Face formulas):

- It takes the **smallest and the largest** weight, and stretches the range of whole numbers over them. With 4 bits the whole numbers run from $q_{min} = 0$ to $q_{max} = 15$ here.
- The **scale** $S$ is the size of one whole-number step, in the weight's own units:

$$
S = \frac{\text{largest} - \text{smallest}}{q_{max} - q_{min}}
$$

- The **zero point** $Z$ is the whole number that stands for 0.0:

$$
Z = q_{min} - \text{round}\left(\frac{\text{smallest}}{S}\right)
$$

**How a weight goes in and comes back:**

- **Quantize** a weight $x$ to the whole number $q$: divide by the scale and shift by the zero point.

$$
q = \text{round}\left(\frac{x}{S} + Z\right)
$$

- **Dequantize** $q$ back to an approximate weight. The difference from the original is the **rounding error**.

$$
x \approx S \cdot (q - Z)
$$

**What is stored:** one small whole number $q$ per weight, and $S$ and $Z$ **once for the whole group** of weights.

**Two variations:**

- **Symmetric quantization** fixes $Z$ at 0 and stores only the scale (Hugging Face). llama.cpp's older `_0` types work this way: weight = scale × $q$ (Hugging Face Hub documentation).
- **Groups of weights.** Hugging Face explains that **one outlier stretches the range** for every weight that shares its scale, so most low-bit methods give each **group of consecutive weights** its own scale. A llama.cpp Q4_0 block holds **32 weights** (ggml-common.h).

**The shared numbers cost bits too:**

- A Q4_0 block stores one 16-bit scale and 32 values of 4 bits (ggml-common.h).
- Bits per weight = (16 + 32 × 4) ÷ 32 = 144 ÷ 32 = **4.5**, not 4.

## Worked Example

**The four weights of the figure, quantized to 4 bits and back by hand.** They are an example, not from a trained model.

The weights: **0.23, −0.6, 0.46 and 0.9**. With 4 bits, $q_{min} = 0$ and $q_{max} = 15$.

1. **Find the range.** Smallest = −0.6, largest = 0.9.
2. **Scale.** $S = (0.9 - (-0.6)) \div (15 - 0) = 1.5 \div 15 =$ **0.1**.
3. **Zero point.** $Z = 0 - \text{round}(-0.6 \div 0.1) = 0 - \text{round}(-6) =$ **6**.
4. **Quantize each weight,** $q = \text{round}(x \div 0.1 + 6)$:
   - 0.23 → 2.3 + 6 = 8.3 → **8**
   - −0.6 → −6 + 6 = 0 → **0**
   - 0.46 → 4.6 + 6 = 10.6 → **11**
   - 0.9 → 9 + 6 = 15 → **15**
5. **Dequantize,** $x \approx 0.1 \times (q - 6)$:
   - 8 → 0.1 × 2 = **0.2**
   - 0 → 0.1 × (−6) = **−0.6**
   - 11 → 0.1 × 5 = **0.5**
   - 15 → 0.1 × 9 = **0.9**
6. **Rounding error,** original − restored:
   - 0.23 − 0.2 = **0.03**
   - −0.6 − (−0.6) = **0**
   - 0.46 − 0.5 = **−0.04**
   - 0.9 − 0.9 = **0**
7. **What is stored.** The four whole numbers 8, 0, 11 and 15 take 4 × 4 = **16 bits**, plus $S$ and $Z$ once. In FP16 the same four weights take 4 × 16 = **64 bits**.

**What this means:** rounding to the nearest whole number moves a weight by **at most half a step**, here 0.1 ÷ 2 = 0.05. Here the smallest and largest weights come back exactly, and the two in between come back 0.03 and 0.04 away.

## Memory At Each Precision

**Memory for the weights = parameters × bits per weight ÷ 8.** The result is in bytes; divide by 1,000,000,000 for gigabytes (GB).

**The figure works it for Llama 3.1 8B Instruct, 8,030,261,248 parameters;** the sum behind each bar is printed under the bars.

- **32 bits (FP32):** about **32.1 GB**. **16 bits (FP16 or BF16):** about **16.1 GB**.
- **8 bits (INT8):** about **8.0 GB**. **4 bits:** about **4.0 GB**.

**Checked against measured files** (the llama.cpp quantize README, for Llama 3.1 8B):

- The **original** file is listed at **32.1 GB**, the same as the FP32 line.
- The **Q4_K_M** file is listed at **4.9 GB**, more than the 4.0 GB of the 4-bit line.
- **Why:** the README measures Q4_K_M at **4.8944 bits per weight** on Llama-3.1-8B, not 4. Its blocks also store their scales (4.5 bits per weight for a Q4_K block, ggml-common.h), and llama-quant.cpp keeps some tensors at 6 bits.
- **Check:** 8,030,261,248 × 4.8944 ÷ 8 ≈ 4,912,913,832 bytes ≈ **4.9 GB**.

**What this means:** each halving of the bits halves the memory for the weights. Hugging Face states the same for bitsandbytes: 8-bit **halves** memory use, and 4-bit **reduces it by 4x**.

## What Is Lost

**llama.cpp measures the loss as a rise in perplexity.**

- **Perplexity** is, in Hugging Face's definition, the exponentiated average negative log-likelihood of a text: a score of how well a model predicts each next token of real text.
- **A higher perplexity** means the model gave the actual next tokens a **lower probability** on average.
- **The table** copies the increase that llama.cpp's quantize tool lists for each file type, measured on **Llama-3-8B** (quantize.cpp). The starting perplexity is not given there, so the table shows the increase only.

**Reading the table:**

- **Fewer bits, larger increase:** +0.0026 for Q8_0, +0.1754 for Q4_K_M, +3.5199 for Q2_K.
- **The letters after the number matter too:** at 4 bits, Q4_K_M adds +0.1754, less than Q4_K_S (+0.2689) and Q4_0 (+0.4685).
- The names are decoded on [Reading a quantized file name](/models/quantization/reading-quantized-file-names).

## What To Watch

- **"4-bit" is not 4 bits per weight.** The shared scales add bits: Q4_0 blocks take 4.5 bits per weight (ggml-common.h), and a whole Q4_K_M file measured 4.8944 on Llama-3.1-8B (llama.cpp README).
- **The memory above is for the weights only.** Running a model also takes memory for its working values and the attention cache; the Hardware needed section of each model page adds 20% for them.
- **Quantize from the original, not from a quantized file.** The llama.cpp README warns that requantizing "can severely reduce quality compared to quantizing from 16bit or 32bit".
- **Smaller does not always mean faster.** Hugging Face notes that weight-only methods save memory but **don't always run faster**, because turning the whole numbers back into weights adds work to every matrix multiplication.
