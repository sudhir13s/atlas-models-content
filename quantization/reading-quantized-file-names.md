---
id: quantization-topic/reading-quantized-file-names
type: quantization-topic
slug: reading-quantized-file-names
schema_version: 2.6
title: "Reading a Quantized File Name"
order: 2
summary: "What the letters and numbers in a quantized model file name such as Q4_K_M mean, and what the other quantized formats on Hugging Face are."
visuals:
  - kind: name_decoder
    section: "Reading A Name"
    examples:
      - name: "Q4_K_M"
        parts:
          - { token: "Q", explains: "A quantized type: each weight is stored as a small whole number q" }
          - { token: "4", explains: "Each q takes 4 bits" }
          - { token: "_K", explains: "A k-quant: super-blocks of 256 weights whose block scales are themselves quantized" }
          - { token: "_M", explains: "A llama.cpp mix: some attention-value and feed-forward-down tensors are stored as Q6_K" }
      - name: "Q8_0"
        parts:
          - { token: "Q", explains: "A quantized type" }
          - { token: "8", explains: "Each q takes 8 bits" }
          - { token: "_0", explains: "Type-0: weight = block scale × q, blocks of 32 weights; 8.5 bits per weight" }
      - name: "Q5_K_S"
        parts:
          - { token: "Q", explains: "A quantized type" }
          - { token: "5", explains: "Each q takes 5 bits" }
          - { token: "_K", explains: "A k-quant: super-blocks of 256 weights" }
          - { token: "_S", explains: "The plainer mix: the main weights stay Q5_K; the output tensor is stored as Q6_K" }
      - name: "IQ4_XS"
        parts:
          - { token: "I", explains: "An i-quant (llama.cpp README); quantize.cpp describes IQ4_XS as non-linear quantization" }
          - { token: "Q", explains: "A quantized type" }
          - { token: "4", explains: "Each q takes 4 bits" }
          - { token: "_XS", explains: "The smaller of llama.cpp's two 4-bit i-quants: 4.25 bits per weight (IQ4_NL: 4.50)" }
      - name: "F16"
        parts:
          - { token: "F", explains: "Floating point: not quantized" }
          - { token: "16", explains: "16 bits: IEEE 754 half precision" }
      - name: "BF16"
        parts:
          - { token: "BF", explains: "Brain floating point (bfloat16): not quantized" }
          - { token: "16", explains: "16 bits, the shortened form of 32-bit single precision" }
    caption: "Six file types from llama.cpp's own list (quantize.cpp), each split into its parts."
tables:
  - section: "Bits Per Weight"
    title: "Bits per weight of each llama.cpp type"
    columns: ["Type", "What one block stores", "Bits per weight"]
    rows:
      - { cells: ["Q8_0", "32 weights: one 16-bit scale + 32 × 8 bits = 272 bits", "272 ÷ 32 = 8.5"], source: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h" }
      - { cells: ["Q5_0", "32 weights: one 16-bit scale + 32 × 5 bits = 176 bits", "176 ÷ 32 = 5.5"], source: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h" }
      - { cells: ["Q4_0", "32 weights: one 16-bit scale + 32 × 4 bits = 144 bits", "144 ÷ 32 = 4.5"], source: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h" }
      - { cells: ["Q6_K", "256 weights in 16 blocks of 16: 256 × 6 bits + 16 block scales of 8 bits + one 16-bit scale = 1,680 bits", "1,680 ÷ 256 = 6.5625"], source: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h" }
      - { cells: ["Q5_K", "256 weights in 8 blocks of 32: 256 × 5 bits + 96 bits of 6-bit block scales and minimums + two 16-bit scales = 1,408 bits", "1,408 ÷ 256 = 5.5"], source: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h" }
      - { cells: ["Q4_K", "256 weights in 8 blocks of 32: 256 × 4 bits + 96 bits of 6-bit block scales and minimums + two 16-bit scales = 1,152 bits", "1,152 ÷ 256 = 4.5"], source: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h" }
      - { cells: ["IQ4_XS", "256 weights: 256 × 4 bits + 48 bits of block scales + one 16-bit scale = 1,088 bits", "1,088 ÷ 256 = 4.25"], source: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h" }
      - { cells: ["Q3_K", "256 weights in 16 blocks of 16: 256 × 3 bits + 96 bits of 6-bit block scales + one 16-bit scale = 880 bits", "880 ÷ 256 = 3.4375"], source: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h" }
      - { cells: ["Q2_K", "256 weights in 16 blocks of 16: 256 × 2 bits + 128 bits of 4-bit block scales and minimums + two 16-bit scales = 672 bits", "672 ÷ 256 = 2.625"], source: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h" }
  - section: "Bits Per Weight"
    title: "Bits per weight of whole files, measured on Llama-3.1-8B"
    columns: ["File type", "Bits per weight"]
    rows:
      - { cells: ["F16", "16.0005"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q8_0", "8.5008"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q6_K", "6.5633"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q5_K_M", "5.7036"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q5_K_S", "5.5704"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q4_K_M", "4.8944"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q4_K_S", "4.6672"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["IQ4_XS", "4.4597"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q3_K_L", "4.2979"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q3_K_M", "3.9960"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q3_K_S", "3.6429"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
      - { cells: ["Q2_K", "3.1593"], source: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md" }
  - section: "Other Formats On Hugging Face"
    title: "Other quantized formats"
    columns: ["Format", "What it is", "Bits"]
    rows:
      - { cells: ["bitsandbytes LLM.int8()", "Quantizes a 16- or 32-bit model to 8 bits as it loads; the outlier feature dimensions are multiplied in 16 bits, while more than 99.9% of values are multiplied in 8 bits", "8"], source: "https://arxiv.org/abs/2208.07339" }
      - { cells: ["bitsandbytes NF4", "4-bit NormalFloat, a 4-bit data type from the QLoRA paper adapted for weights initialized from a normal (bell-shaped) distribution; Hugging Face says to use it for training 4-bit base models", "4"], source: "https://huggingface.co/docs/transformers/quantization/bitsandbytes" }
      - { cells: ["GPTQ", "Quantizes one layer at a time and uses a small calibration dataset to update the remaining weights so they make up for each layer's rounding error; groups of 128 weights share a scale by default", "2, 3, 4 or 8 in Transformers"], source: "https://huggingface.co/docs/transformers/quantization/gptq" }
      - { cells: ["AWQ", "Activation-aware Weight Quantization: preserves a small fraction of the weights that are important for performance, to compress a model to 4 bits", "4"], source: "https://huggingface.co/docs/transformers/quantization/awq" }
      - { cells: ["EXL2", "ExLlamaV2's format, based on the same optimization method as GPTQ; it mixes quantization levels within a model to reach any average between 2 and 8 bits per weight", "2, 3, 4, 5, 6 and 8, mixed"], source: "https://github.com/turboderp-org/exllamav2" }
      - { cells: ["MLX (affine mode)", "Groups of 32, 64 or 128 consecutive weights share a scale and a bias; the defaults are 4 bits and groups of 64", "1, 2, 3, 4, 5, 6 or 8"], source: "https://ml-explore.github.io/mlx/build/html/python/_autosummary/mlx.core.quantize.html" }
      - { cells: ["Unsloth Dynamic (UD-)", "GGUF files in which Unsloth chooses the quantization type layer by layer; the file names carry UD- before the type, as in UD-Q2_K_XL", "varies by layer"], source: "https://docs.unsloth.ai/basics/unsloth-dynamic-2.0-ggufs" }
tags: [quantization, gguf, llama-cpp, naming, file-formats]
updated: 2026-11-23
related: [quantization-topic/number-formats, hyperparameter/precision]
sources:
  - { url: "https://github.com/ggml-org/ggml/blob/master/docs/gguf.md", title: "ggml: GGUF file format specification (docs/gguf.md)", trust: 0.95 }
  - { url: "https://huggingface.co/docs/hub/gguf", title: "Hugging Face Hub GGUF documentation (quantization types)", trust: 0.9 }
  - { url: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md", title: "llama.cpp quantize README: tools/quantize/README.md (llama-quantize)", trust: 0.95 }
  - { url: "https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/quantize.cpp", title: "llama.cpp quantize.cpp: the list of file types (tools/quantize/quantize.cpp)", trust: 0.95 }
  - { url: "https://github.com/ggml-org/llama.cpp/blob/master/ggml/src/ggml-common.h", title: "llama.cpp ggml-common.h: quantized block layouts (ggml/src/ggml-common.h)", trust: 0.95 }
  - { url: "https://github.com/ggml-org/llama.cpp/blob/master/src/llama-quant.cpp", title: "llama.cpp llama-quant.cpp: which tensors each file type keeps at more bits (src/llama-quant.cpp)", trust: 0.95 }
  - { url: "https://github.com/ggml-org/llama.cpp/pull/1684", title: "llama.cpp pull request #1684: k-quants", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/quantization/concept_guide", title: "Hugging Face Transformers quantization concepts", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/quantization/bitsandbytes", title: "Hugging Face Transformers bitsandbytes documentation", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/quantization/gptq", title: "Hugging Face Transformers GPTQ documentation", trust: 0.9 }
  - { url: "https://huggingface.co/docs/transformers/quantization/awq", title: "Hugging Face Transformers AWQ documentation", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2208.07339", title: "LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2305.14314", title: "QLoRA: Efficient Finetuning of Quantized LLMs", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2210.17323", title: "GPTQ: Accurate Post-Training Quantization for Generative Pre-trained Transformers", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2306.00978", title: "AWQ: Activation-aware Weight Quantization for LLM Compression and Acceleration", trust: 0.9 }
  - { url: "https://github.com/turboderp-org/exllamav2", title: "ExLlamaV2 README (EXL2 quantization)", trust: 0.85 }
  - { url: "https://ml-explore.github.io/mlx/build/html/python/_autosummary/mlx.core.quantize.html", title: "MLX documentation: mlx.core.quantize", trust: 0.9 }
  - { url: "https://github.com/ml-explore/mlx-lm", title: "MLX LM README", trust: 0.85 }
  - { url: "https://docs.unsloth.ai/basics/unsloth-dynamic-2.0-ggufs", title: "Unsloth documentation: Unsloth Dynamic GGUFs (Dynamic 2.0 and 3.0)", trust: 0.8 }
---

## What It Is

**A quantized file's name says how its weights were stored.** This page decodes the names of GGUF files, the format llama.cpp uses, and lists the other quantized formats found on Hugging Face.

**GGUF, the file format:**

- **What it is:** ggml's specification calls GGUF "a file format for storing models for inference" with GGML and the programs built on it.
- **Where it is used:** llama.cpp's quantize tool takes a GGUF file in and writes a quantized GGUF file out (llama.cpp README). The Hugging Face Hub documentation names the same developer for GGUF and llama.cpp.
- **One file holds everything:** the specification lists single-file deployment, and all the information needed to load a model inside the file.
- **What it replaced:** GGUF is the successor to the GGML, GGMF and GGJT formats (the specification).
- **The name:** the specification and the Hub documentation use GGUF without spelling out the letters.
- **On Hugging Face:** the Hub has a viewer that shows a GGUF file's metadata and its tensors, each with its name, shape and precision.

**The GGUF naming convention** (the specification) puts the parts in this order, joined by hyphens:

- `BaseName-SizeLabel-FineTune-Version-Encoding-Type-Shard.gguf`
- **SizeLabel** is the parameter count with a letter: **B** for billion, **M** for million.
- **Encoding** is the weight encoding, such as `Q4_K_M`; it is what the rest of this page decodes.
- The specification's own example: `Grok-100B-v1.0-Q4_0-00003-of-00009.gguf` is the model Grok, **100B** parameters, version **v1.0**, encoding **Q4_0**, shard **3 of 9**.
- The specification adds that the convention is **not intended to be perfectly parsable**, because real file names vary.

## Reading A Name

**Every part of a llama.cpp type name says something about how a weight is stored.** The cards above split six names from llama.cpp's own list of file types.

**Q and the number:**

- **Q** marks a quantized type: each weight is stored as a small whole number, written $q$ in llama.cpp's formulas.
- **The number** is the bits of each $q$: the Hugging Face Hub table describes Q4_K as **4-bit quantization**, Q6_K as **6-bit**.
- **It is not the bits per weight of the file.** Each block also stores scales, so a Q4_K block uses **4.5 bits per weight** (ggml-common.h). The next section gives the figures.

**_0 and _1, the older types** (the Hub calls them legacy):

- llama.cpp pull request #1684 calls them **type-0** and **type-1**.
- **Type-0** (`Q4_0`, `Q5_0`, `Q8_0`): weight = $d \times q$, where $d$ is the block's scale.
- **Type-1** (`Q4_1`, `Q5_1`): weight = $d \times q + m$, where $m$ is the block's minimum.
- **Each block holds 32 weights** (ggml-common.h).

**_K, the k-quants:**

- llama.cpp added them in pull request #1684, titled **"k-quants"**.
- **Super-blocks of 256 weights** (`QK_K = 256` in ggml-common.h), split into blocks of 16 or 32.
- **The block scales are quantized too,** to 6 bits in Q4_K and Q5_K and to 8 bits in Q6_K (Hugging Face Hub table; ggml-common.h). That is what keeps the extra bits per weight small.

**_S, _M and _L, the mixes:**

- A llama.cpp file type is a **mix of tensor types**. The README's `--pure` option turns off the "k-quant mixtures" and quantizes every tensor to the same type.
- **_M and _L keep more tensors at higher types than _S.** In llama-quant.cpp, Q4_K_M stores the attention-value (`attn_v`) and feed-forward-down (`ffn_down`) tensors as Q6_K in some layers: the first and last eighth of the layers, and every third layer between them.
- **Q3_K_L** stores those two kinds of tensor as Q5_K (llama-quant.cpp). Q5_K_S keeps the main weights at Q5_K.
- **The output tensor** of a k-quant file is normally stored as Q6_K (llama-quant.cpp).
- **The measured result:** on Llama-3.1-8B the README lists Q3_K_S at 3.6429, Q3_K_M at 3.9960 and Q3_K_L at 4.2979 bits per weight.
- **What the letters stand for** is not spelled out in the llama.cpp sources fetched for this page.

**IQ, the i-quants:**

- The llama.cpp README lists them as **i-quants**; quantize.cpp describes IQ4_XS and IQ4_NL as **non-linear quantization**.
- The Hugging Face Hub table says an IQ weight is obtained using the super-block scale and an **importance matrix**.
- `llama-quantize` takes an importance matrix file with `--imatrix` and uses it for quantization optimizations (README).
- **XXS, XS, S and M** after IQ are sizes of the same bit count: on Llama-3.1-8B the README lists IQ2_XXS at 2.3824, IQ2_XS at 2.5882, IQ2_S at 2.7403 and IQ2_M at 2.9294 bits per weight.

**F16, BF16 and F32 are not quantized** (Hugging Face Hub table):

- **F16:** 16-bit IEEE 754 half precision.
- **BF16:** a shortened 16-bit version of 32-bit single precision.
- **F32:** 32-bit IEEE 754 single precision. The bit layouts are on [Number formats](/models/quantization/number-formats).

## Bits Per Weight

**Two counts: one block of a type, and a whole file.**

**One block** (the first table): bits per weight = all the bits a block stores ÷ the weights it holds.

- The bits come from the block layouts in **ggml-common.h**; each row shows the sum and the division.
- The k-quant results match the comments in that file ("Effectively 4.5 bits per weight" for Q4_K), and IQ4_XS matches the 4.25 that quantize.cpp lists.

**A whole file** (the second table): the llama.cpp README measures each file type on **Llama-3.1-8B**.

- A whole file uses **more bits per weight than its base type**: Q4_K_M measures 4.8944 against 4.5 for a Q4_K block, because of the tensors it keeps at higher types (llama-quant.cpp).
- **Memory for the weights** = parameters × bits per weight ÷ 8, worked through on [Number formats](/models/quantization/number-formats).

## Other Formats On Hugging Face

**Formats a reader meets on Hugging Face besides GGUF, each in one line from its own documentation.**

- **bitsandbytes** quantizes a model while it loads, with no calibration data (Hugging Face Quantization concepts). Its 8-bit method is LLM.int8() and its 4-bit data type is NF4.
- **GPTQ and AWQ** use calibration data: a small dataset is run through the model to decide how to quantize each layer (Hugging Face Quantization concepts).
  - Within a layer, GPTQ quantizes one column at a time and adjusts the remaining columns to make up for each column's rounding error.
  - AWQ finds the weight channels with the largest activations and scales them up before rounding, to protect them.
- **How to tell an AWQ checkpoint:** Hugging Face says to check the `quant_method` key in the model's `config.json`.
- **EXL2** chooses its mix automatically: the ExLlamaV2 README says it quantizes each matrix several times and picks a combination that meets a target average bitrate.
- **MLX** runs models on Apple silicon (MLX LM README). The README's example repositories carry the bit count in their names, as in `mlx-community/Mistral-7B-Instruct-v0.3-4bit`.
- **Unsloth Dynamic:** for Dynamic 2.0, Unsloth writes that it adjusts the quantization type of every possible layer, and that the combination differs for each layer and model.
  - **_XL** in a name such as `UD-Q2_K_XL` is not one of llama.cpp's own file types (quantize.cpp lists none); it comes from the publisher.

## What To Watch

- **The number after Q is not the file's bits per weight.** Q4_K_M measured **4.8944** on Llama-3.1-8B (llama.cpp README).
- **Two files with the same type can differ.** The GGUF specification says the content, type mixture and arrangement of an encoding "are determined by user code and can vary".
- **An importance matrix changes the result, not the name.** The README says a suitable imatrix file can reduce the accuracy loss; its examples ask for the same type, `q4_k_m`, with and without `--imatrix`.
- **Names outside llama.cpp's list** (such as `UD-` and `_XL`) are the publisher's own; read the publisher's page for what they mean.
