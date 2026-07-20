---
id: architecture-era/flashattention
type: architecture-era
slug: flashattention
schema_version: 1.0
title: "FlashAttention"
name: "FlashAttention"
era: post-transformer
era_range: "2022–2024"
year: 2022
family: llm
core_innovation: "An IO-aware, tiled attention kernel that computes exact attention without ever materializing the full N×N score matrix in slow memory."
description: "The GPU-memory-aware attention kernel that made long-context transformers fast and memory-cheap."
key_features: ["IO-aware tiling", "Online softmax (no full score matrix)", "Fused kernel, fewer HBM reads/writes", "Exact (not approximate) attention", "Linear memory in sequence length"]
key_models: ["FlashAttention (2022)", "FlashAttention-2 (2023)", "FlashAttention-3 (2024)"]
paper_title: "FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness"
paper_url: "https://arxiv.org/abs/2205.14135"
influenced_by: [architecture-era/transformer]
led_to: []

eli5: "Regular attention writes a giant table of every-word-versus-every-word into slow memory, then reads it back — and that shuttling is the slow part. FlashAttention does the whole calculation in a tiny patch of ultra-fast memory, block by block, and never writes the giant table at all. Same answer, much faster, far less memory."

analogy:
  title: "Doing sums on a sticky note, not a filing cabinet"
  body: "Imagine adding up a huge ledger. The slow way: copy every intermediate subtotal into a giant spreadsheet stored in a filing cabinet across the room, then walk back and forth fetching pages — the walking, not the adding, is what kills you. FlashAttention keeps a tiny running total on a sticky note right in front of you: it reads one small block of numbers, updates the running total, and moves on, never storing the full spreadsheet. The filing cabinet is the GPU's slow high-bandwidth memory (HBM); the sticky note is the fast on-chip SRAM; the running total is the online softmax."

narrative:
  motivation: "Attention's textbook recipe builds an N×N matrix of scores between every pair of tokens, softmaxes it, and multiplies by the values. Everyone assumed the cost was the arithmetic. It isn't — the real bottleneck is memory traffic: that giant intermediate matrix gets written to, and read back from, the GPU's slow high-bandwidth memory (HBM), and its O(N²) size caps how long a sequence you can even fit."
  approach: "Be IO-aware: minimize slow-memory traffic instead of counting arithmetic operations. GPUs have a small pool of extremely fast on-chip memory (SRAM) and a large pool of slow HBM. The winning move is to keep the working data in SRAM and never write the full score matrix to HBM at all — computing attention in blocks small enough to fit on-chip."
  solution: "FlashAttention tiles the queries, keys, and values into blocks that fit in SRAM and streams through them. An online softmax keeps a running maximum and running sum so it can accumulate the correct softmax-weighted output block by block, rescaling as it goes — without ever holding the whole N×N matrix. The whole thing is fused into a single GPU kernel, so intermediates stay on-chip and HBM reads/writes plummet."
  outcome: "The output is bit-for-bit the exact same attention (no approximation), but faster and with memory that grows linearly, not quadratically, in sequence length. That single kernel is a big part of why context windows jumped from a couple thousand tokens to tens or hundreds of thousands. FlashAttention-2 and -3 pushed GPU utilization further; the kernel is now standard infrastructure inside virtually every large model's training and inference stack."

build_steps:
  - { title: "1. See the real cost", detail: "Profile attention and notice the FLOPs are cheap; the time goes to writing and reading the N×N score matrix in slow HBM. The problem is memory movement, not math." }
  - { title: "2. Split Q, K, V into blocks", detail: "Tile the queries, keys, and values into blocks small enough that a working set fits inside the GPU's fast on-chip SRAM." }
  - { title: "3. Stream key/value blocks past each query block", detail: "For a block of queries, loop over the key/value blocks, computing partial scores and partial outputs one block at a time — all in SRAM." }
  - { title: "4. Keep an online softmax", detail: "Maintain a running maximum and running normalizer so each new block's contribution is correctly rescaled into the accumulating output. The full softmax is computed without ever storing the full score row." }
  - { title: "5. Fuse into one kernel", detail: "Do scoring, softmax, and the value-weighting inside a single fused GPU kernel so intermediates never touch HBM. Only the final output is written out." }
  - { title: "6. Recompute in the backward pass", detail: "During training, don't store the giant attention matrix for the gradient — recompute blocks on the fly. Extra arithmetic is far cheaper than the memory traffic it avoids." }

key_equation:
  latex: "m_i = \\max(m_{i-1}, \\tilde{m}_i), \\quad \\ell_i = e^{m_{i-1}-m_i}\\,\\ell_{i-1} + e^{\\tilde{m}_i - m_i}\\,\\tilde{\\ell}_i, \\quad O_i = e^{m_{i-1}-m_i}\\,O_{i-1} + p_i V_i"
  plain: "The online-softmax recurrence: as each new block arrives, update the running maximum m, rescale the running normalizer ℓ and running output O so they stay consistent with the new maximum, then add the new block's contribution. This lets the exact softmax-weighted output be built incrementally, so the full N×N score matrix never has to exist in memory."
  terms:
    - { symbol: "m_i", meaning: "running maximum score after processing block i — used to keep the exponentials numerically stable" }
    - { symbol: "ℓ_i", meaning: "running softmax normalizer (sum of exponentials) after block i" }
    - { symbol: "O_i", meaning: "running attention output accumulated through block i" }
    - { symbol: "p_i V_i", meaning: "the current block's softmax-weighted values being added to the running output" }
    - { symbol: "e^{m_{i-1}-m_i}", meaning: "the rescale factor that corrects earlier partial results when a larger maximum appears" }

strengths:
  - "Exact attention — bit-for-bit identical output, so it's a drop-in replacement, not an approximation."
  - "Memory grows linearly in sequence length instead of quadratically, unlocking very long contexts."
  - "Large real-world speedups on GPUs by slashing slow-memory (HBM) traffic and fusing the kernel."
  - "Benefits both training and inference, and composes with other efficiency tricks."

weaknesses:
  - "It's a hand-written, hardware-specific kernel — tuned per GPU generation, hard to port and maintain."
  - "It optimizes exact attention, so it can't beat the asymptotic wins of genuinely sub-quadratic methods on extreme lengths."
  - "Correctness hinges on subtle numerics (the online-softmax rescaling); bugs are easy and painful."
  - "Best speedups need specific hardware features (e.g. tensor cores, newer memory hierarchies)."

diagram: |
  flowchart TD
    Q["Query block (in SRAM)"] --> C["Compute block scores"]
    KV["Stream K,V blocks from HBM"] --> C
    C --> OS["Online softmax: update running max and sum"]
    OS --> ACC["Accumulate output in SRAM"]
    ACC --> MORE{"More K,V blocks?"}
    MORE -->|"yes"| KV
    MORE -->|"no"| WRITE["Write final output to HBM"]
diagram_caption: "Query blocks stay in fast SRAM while key/value blocks stream past; an online softmax accumulates the exact result on-chip, so the full N×N score matrix is never written to slow HBM."

notable_models:
  - { name: "FlashAttention", year: 2022, note: "Reframed attention as an IO-bound problem and delivered exact attention with linear memory via tiling plus online softmax." }
  - { name: "FlashAttention-2", year: 2023, note: "Reworked the parallelism and work partitioning to reach a much higher fraction of the GPU's peak throughput." }
  - { name: "FlashAttention-3", year: 2024, note: "Exploited newer GPU features (asynchrony and low-precision) to push utilization higher on Hopper-class hardware." }

resources:
  - { kind: paper, title: "FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness (Dao et al., 2022)", url: "https://arxiv.org/abs/2205.14135" }
  - { kind: paper, title: "FlashAttention-2: Faster Attention with Better Parallelism and Work Partitioning (Dao, 2023)", url: "https://arxiv.org/abs/2307.08691" }
  - { kind: blog, title: "ELI5: FlashAttention (Aleksa Gordić)", url: "https://gordicaleksa.medium.com/eli5-flash-attention-5c44017022ad" }
  - { kind: blog, title: "Online normalizer calculation for softmax (Milakov & Gimelshein, 2018)", url: "https://arxiv.org/abs/1805.02867" }

learn_first:
  - { label: "Attention & the softmax it depends on", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "Softmax & numerical stability (the max-subtraction trick)", href: "/learn/concepts/softmax", tag: "Math" }
  - { label: "GPU memory hierarchy — SRAM vs. HBM", href: "/learn/concepts/gpu-memory", tag: "Hardware" }

tags: [flashattention, attention, gpu, systems, long-context]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2205.14135", title: "FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness (Dao et al., 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2307.08691", title: "FlashAttention-2 (Dao, 2023)", trust: 0.9 }
---

## What Changed
This entry is a *systems* advance, not a new model architecture — but it reshaped what transformers can
do, so it belongs in the timeline. Standard attention computes an $N \times N$ score matrix, applies
softmax, and multiplies by values. The bottleneck is not the arithmetic but the **memory traffic**:
that giant intermediate matrix is written to and read from the GPU's slow high-bandwidth memory (HBM),
and its $O(N^2)$ size caps how long a sequence you can process. FlashAttention makes attention
**IO-aware**. It tiles the computation into blocks that fit in fast on-chip SRAM, uses an **online
softmax** to accumulate the result block by block without ever storing the full score matrix, and
fuses everything into one kernel — dramatically cutting HBM reads/writes. The result is the *exact*
same attention output (no approximation), but faster and with memory that grows *linearly*, not
quadratically, in sequence length. That single kernel is a big part of why context windows jumped from
a couple thousand tokens to tens or hundreds of thousands. It sits on the transformer branch as
critical enabling infrastructure rather than a change to the model itself.

## Key Ideas
- **IO-awareness** — the true cost of attention on a GPU is memory movement, not FLOPs; optimising
  HBM traffic is what wins, an insight from hardware, not model design.
- **Tiling + online softmax** — process attention in SRAM-sized blocks and update a running softmax so
  the full $N\times N$ matrix never needs to exist.
- **Exact, not approximate** — unlike sparse/linear attention variants, FlashAttention returns the
  identical result to standard attention, so it is a drop-in speed/memory win.
- **Enables long context** — linear memory in sequence length is a primary reason modern LLMs handle
  very long inputs; the advance is infrastructure, not architecture.
