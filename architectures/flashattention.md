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
  outcome: "The output is exact attention (no approximation; only the order of floating-point operations differs), but faster and with memory that grows linearly, not quadratically, in sequence length. That single kernel is a big part of why context windows jumped from a couple thousand tokens to tens or hundreds of thousands. FlashAttention-2 and -3 pushed GPU utilization further; the kernel is now standard infrastructure inside virtually every large model's training and inference stack."

build_steps:
  - { title: "1. See the real cost", detail: "Profile attention and notice the FLOPs are cheap; the time goes to writing and reading the N×N score matrix in slow HBM. The problem is memory movement, not math." }
  - { title: "2. Split Q, K, V into blocks", detail: "Tile the queries, keys, and values into blocks small enough that a working set fits inside the GPU's fast on-chip SRAM." }
  - { title: "3. Visit every pair of blocks", detail: "Load one key/value block into SRAM, then pass each query block by it, computing that tile's partial scores and partial output entirely in SRAM. The 2022 paper loops over key/value blocks on the outside and query blocks inside; FlashAttention-2 swaps the two loops." }
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
  - "Exact attention — the same mathematical result, so it's a drop-in replacement, not an approximation."
  - "Memory grows linearly in sequence length instead of quadratically, unlocking very long contexts."
  - "Large real-world speedups on GPUs by slashing slow-memory (HBM) traffic and fusing the kernel."
  - "Benefits both training and inference, and composes with other efficiency tricks."

weaknesses:
  - "It's a hand-written, hardware-specific kernel — tuned per GPU generation, hard to port and maintain."
  - "It optimizes exact attention, so it can't beat the asymptotic wins of genuinely sub-quadratic methods on extreme lengths."
  - "Correctness hinges on subtle numerics (the online-softmax rescaling); bugs are easy and painful."
  - "Best speedups need specific hardware features (e.g. tensor cores, newer memory hierarchies)."

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 548" role="img" aria-label="Standard attention writes the N by N matrices S and P to slow GPU memory (HBM); FlashAttention instead loops over blocks of Q, K and V, computes one tile of the N by N score matrix at a time in fast on-chip SRAM with running softmax statistics, and writes back only the output and two length-N statistics, giving the exact same output with memory linear in N.">
  <defs><marker id="flashattention-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="flashattention-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <text class="d-title" x="126" y="22" text-anchor="middle">Standard attention</text>
  <rect class="d-box" x="16" y="40" width="220" height="404" rx="6"/>
  <text class="d-label" x="28" y="60" text-anchor="start">HBM (slow, large)</text>
  <rect class="d-box d-role-input" x="46" y="76" width="12" height="44"/>
  <text class="d-note" x="52" y="134" text-anchor="middle">Q</text>
  <rect class="d-box d-role-input" x="68" y="76" width="12" height="44"/>
  <text class="d-note" x="74" y="134" text-anchor="middle">K</text>
  <rect class="d-box d-role-input" x="90" y="76" width="12" height="44"/>
  <text class="d-note" x="96" y="134" text-anchor="middle">V</text>
  <text class="d-note" x="162" y="102" text-anchor="middle">N × d each</text>
  <path class="d-line-strong" d="M82 140 V164" marker-end="url(#flashattention-arrow)"/>
  <text class="d-note" x="92" y="156" text-anchor="start">S = QKᵀ, write S</text>
  <rect class="d-box d-role-router" x="46" y="170" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="170" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="170" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="170" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="170" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="170" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="182" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="182" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="182" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="182" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="182" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="182" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="194" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="194" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="194" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="194" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="194" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="194" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="206" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="206" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="206" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="206" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="206" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="206" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="218" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="218" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="218" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="218" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="218" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="218" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="230" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="230" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="230" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="230" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="230" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="230" width="12" height="12"/>
  <text class="d-label" x="130" y="198" text-anchor="start">S: N × N</text>
  <text class="d-note" x="130" y="214" text-anchor="start">stored in HBM</text>
  <path class="d-line-strong" d="M82 248 V284" marker-end="url(#flashattention-arrow)"/>
  <text class="d-note" x="92" y="268" text-anchor="start">read S, softmax, write P</text>
  <rect class="d-box d-role-router" x="46" y="290" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="290" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="290" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="290" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="290" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="290" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="302" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="302" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="302" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="302" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="302" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="302" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="314" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="314" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="314" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="314" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="314" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="314" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="326" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="326" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="326" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="326" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="326" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="326" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="338" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="338" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="338" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="338" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="338" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="338" width="12" height="12"/>
  <rect class="d-box d-role-router" x="46" y="350" width="12" height="12"/>
  <rect class="d-box d-role-router" x="58" y="350" width="12" height="12"/>
  <rect class="d-box d-role-router" x="70" y="350" width="12" height="12"/>
  <rect class="d-box d-role-router" x="82" y="350" width="12" height="12"/>
  <rect class="d-box d-role-router" x="94" y="350" width="12" height="12"/>
  <rect class="d-box d-role-router" x="106" y="350" width="12" height="12"/>
  <text class="d-label" x="130" y="318" text-anchor="start">P: N × N</text>
  <text class="d-note" x="130" y="334" text-anchor="start">stored in HBM</text>
  <path class="d-line-strong" d="M82 368 V400" marker-end="url(#flashattention-arrow)"/>
  <text class="d-note" x="92" y="388" text-anchor="start">read P and V, O = PV</text>
  <rect class="d-box d-role-output" x="46" y="406" width="72" height="12"/>
  <text class="d-note" x="130" y="416" text-anchor="start">O: N × d</text>
  <text class="d-label" x="126" y="466" text-anchor="middle">Memory O(N²)</text>
  <text class="d-note" x="126" y="482" text-anchor="middle">HBM accesses Θ(Nd + N²)</text>
  <text class="d-title" x="452" y="22" text-anchor="middle">FlashAttention</text>
  <path class="d-line-strong" d="M336 48 H544" marker-end="url(#flashattention-arrow)"/>
  <text class="d-note" x="440" y="40" text-anchor="middle">outer loop over j: load K<tspan class="d-note" dy="3">j</tspan><tspan dy="-3">​</tspan>, V<tspan class="d-note" dy="3">j</tspan><tspan dy="-3">​</tspan> to SRAM</text>
  <text class="d-label" x="328" y="113" text-anchor="end">Kᵀ</text>
  <rect class="d-box d-role-input" x="336" y="100" width="52" height="18"/>
  <rect class="d-box d-role-input" x="388" y="100" width="52" height="18"/>
  <rect class="d-box d-role-attention" x="440" y="100" width="52" height="18"/>
  <rect class="d-box d-role-input" x="492" y="100" width="52" height="18"/>
  <text class="d-note" x="466" y="113" text-anchor="middle">K<tspan class="d-note" dy="3">j</tspan><tspan dy="-3">​</tspan>ᵀ</text>
  <rect class="d-box d-role-input" x="302" y="128" width="18" height="52"/>
  <rect class="d-box d-role-attention" x="302" y="180" width="18" height="52"/>
  <rect class="d-box d-role-input" x="302" y="232" width="18" height="52"/>
  <rect class="d-box d-role-input" x="302" y="284" width="18" height="52"/>
  <text class="d-label" x="311" y="354" text-anchor="middle">Q</text>
  <text class="d-note" x="311" y="210" text-anchor="middle">Q<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan></text>
  <path class="d-line-strong" d="M290 128 V336" marker-end="url(#flashattention-arrow)"/>
  <text class="d-note" x="0" y="0" text-anchor="middle" transform="translate(280 232) rotate(-90)">inner loop over i: load Q<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, O<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, ℓ<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, m<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan></text>
  <rect class="d-line" x="336" y="128" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="362" y="128" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="388" y="128" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="414" y="128" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="440" y="128" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="466" y="128" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="492" y="128" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="518" y="128" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="336" y="154" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="362" y="154" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="388" y="154" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="414" y="154" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="440" y="154" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="466" y="154" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="492" y="154" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="518" y="154" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="336" y="180" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="362" y="180" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="388" y="180" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="414" y="180" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="440" y="180" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="466" y="180" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="492" y="180" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="518" y="180" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="336" y="206" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="362" y="206" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="388" y="206" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="414" y="206" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="440" y="206" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="466" y="206" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="492" y="206" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="518" y="206" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="336" y="232" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="362" y="232" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="388" y="232" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="414" y="232" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="440" y="232" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="466" y="232" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="492" y="232" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="518" y="232" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="336" y="258" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="362" y="258" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="388" y="258" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="414" y="258" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="440" y="258" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="466" y="258" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="492" y="258" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="518" y="258" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="336" y="284" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="362" y="284" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="388" y="284" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="414" y="284" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="440" y="284" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="466" y="284" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="492" y="284" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="518" y="284" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="336" y="310" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="362" y="310" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="388" y="310" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="414" y="310" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="440" y="310" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="466" y="310" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="492" y="310" width="26" height="26" stroke-dasharray="2 3"/>
  <rect class="d-line" x="518" y="310" width="26" height="26" stroke-dasharray="2 3"/>
  <path class="d-line" d="M388 128 V336 M336 180 H544"/>
  <path class="d-line" d="M440 128 V336 M336 232 H544"/>
  <path class="d-line" d="M492 128 V336 M336 284 H544"/>
  <rect class="d-box d-role-attention" x="440" y="180" width="52" height="52"/>
  <text class="d-label" x="466" y="210" text-anchor="middle">S<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan></text>
  <path class="d-line-accent" d="M492 206 C 620 206, 600 158, 642 158" marker-end="url(#flashattention-arrow-accent)"/>
  <rect class="d-box d-role-output" x="560" y="128" width="18" height="52"/>
  <rect class="d-box d-role-attention" x="560" y="180" width="18" height="52"/>
  <rect class="d-box d-role-output" x="560" y="232" width="18" height="52"/>
  <rect class="d-box d-role-output" x="560" y="284" width="18" height="52"/>
  <text class="d-label" x="569" y="354" text-anchor="middle">O</text>
  <text class="d-note" x="569" y="210" text-anchor="middle">O<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan></text>
  <rect class="d-box d-role-input" x="336" y="364" width="52" height="18"/>
  <rect class="d-box d-role-input" x="388" y="364" width="52" height="18"/>
  <rect class="d-box d-role-attention" x="440" y="364" width="52" height="18"/>
  <rect class="d-box d-role-input" x="492" y="364" width="52" height="18"/>
  <text class="d-label" x="328" y="377" text-anchor="end">Vᵀ</text>
  <text class="d-note" x="466" y="377" text-anchor="middle">V<tspan class="d-note" dy="3">j</tspan><tspan dy="-3">​</tspan></text>
  <text class="d-label" x="440" y="410" text-anchor="middle">S = QKᵀ (N × N) is never stored:</text>
  <text class="d-note" x="440" y="426" text-anchor="middle">only the B<tspan class="d-note" dy="3">r</tspan><tspan dy="-3">​</tspan> × B<tspan class="d-note" dy="3">c</tspan><tspan dy="-3">​</tspan> tile S<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan> exists, inside SRAM</text>
  <text class="d-title" x="733" y="22" text-anchor="middle">Where each tile is computed</text>
  <rect class="d-box d-role-attention" x="646" y="92" width="174" height="132" rx="6"/>
  <text class="d-title" x="733" y="110" text-anchor="middle">On-chip SRAM</text>
  <text class="d-note" x="733" y="125" text-anchor="middle">≈20 MB · 19 TB/s (fast, small)</text>
  <text class="d-math" x="658" y="148" text-anchor="start">S<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan> = Q<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan> K<tspan class="d-note" dy="3">j</tspan><tspan dy="-3">​</tspan>ᵀ</text>
  <text class="d-math" x="658" y="166" text-anchor="start">m̃<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan> = rowmax(S<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan>)</text>
  <text class="d-math" x="658" y="184" text-anchor="start">P̃<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan> = exp(S<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan> − m̃<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan>)</text>
  <text class="d-math" x="658" y="202" text-anchor="start">rescale O<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, add P̃<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan> V<tspan class="d-note" dy="3">j</tspan><tspan dy="-3">​</tspan></text>
  <rect class="d-box" x="622" y="300" width="222" height="144" rx="6"/>
  <text class="d-title" x="733" y="318" text-anchor="middle">HBM</text>
  <text class="d-note" x="733" y="333" text-anchor="middle">40 GB · 1.5 TB/s (slow, large)</text>
  <rect class="d-box d-role-input" x="658" y="350" width="12" height="52"/>
  <text class="d-note" x="664" y="418" text-anchor="middle">Q</text>
  <rect class="d-box d-role-input" x="684" y="350" width="12" height="52"/>
  <text class="d-note" x="690" y="418" text-anchor="middle">K</text>
  <rect class="d-box d-role-input" x="710" y="350" width="12" height="52"/>
  <text class="d-note" x="716" y="418" text-anchor="middle">V</text>
  <rect class="d-box d-role-output" x="736" y="350" width="12" height="52"/>
  <text class="d-note" x="742" y="418" text-anchor="middle">O</text>
  <rect class="d-box d-role-output" x="778" y="350" width="6" height="52"/>
  <text class="d-note" x="781" y="418" text-anchor="middle">ℓ</text>
  <rect class="d-box d-role-output" x="800" y="350" width="6" height="52"/>
  <text class="d-note" x="803" y="418" text-anchor="middle">m</text>
  <text class="d-note" x="733" y="436" text-anchor="middle">only N × d and length-N arrays</text>
  <path class="d-line-strong" d="M660 296 V230" marker-end="url(#flashattention-arrow)"/>
  <path class="d-line-strong" d="M806 228 V294" marker-end="url(#flashattention-arrow)"/>
  <text class="d-note" x="733" y="254" text-anchor="middle">load K<tspan class="d-note" dy="3">j</tspan><tspan dy="-3">​</tspan>, V<tspan class="d-note" dy="3">j</tspan><tspan dy="-3">​</tspan>, Q<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, O<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, ℓ<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, m<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan></text>
  <text class="d-note" x="733" y="272" text-anchor="middle">write back O<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, ℓ<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, m<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan></text>
  <text class="d-note" x="733" y="290" text-anchor="middle">(never S or P)</text>
  <path class="d-line" d="M16 498 H844" opacity="0.6"/>
  <text class="d-math" x="430" y="516" text-anchor="middle">running softmax per row block:  m<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>ⁿᵉʷ = max(m<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>, m̃<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan>)    ℓ<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>ⁿᵉʷ = exp(m<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan> − m<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>ⁿᵉʷ)·ℓ<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan> + exp(m̃<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan> − m<tspan class="d-note" dy="3">i</tspan><tspan dy="-3">​</tspan>ⁿᵉʷ)·rowsum(P̃<tspan class="d-note" dy="3">ij</tspan><tspan dy="-3">​</tspan>)</text>
  <text class="d-label" x="430" y="534" text-anchor="middle">Result: the exact O = softmax(QKᵀ)V, extra memory O(N) instead of O(N²), HBM accesses Θ(N²d²/M) for SRAM size M</text>
  </svg>
diagram_caption: "Standard attention stores the N×N matrices S and P in slow HBM; FlashAttention computes one tile of S at a time in on-chip SRAM with running softmax statistics and writes back only O, ℓ and m: exact output, O(N) extra memory."
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
