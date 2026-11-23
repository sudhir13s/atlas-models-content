---
id: architecture-era/flashattention
type: architecture-era
slug: flashattention
schema_version: 3.1
title: FlashAttention
name: FlashAttention
era: post-transformer
era_range: 2022–2024
year: 2022
family: llm
core_innovation: An IO-aware, tiled attention kernel that computes exact attention without ever materializing the full N×N score matrix in slow memory.
description: A way of computing a Transformer's attention on a GPU that gives exactly the same result as the usual method, but works through the inputs in small blocks inside the chip's fast memory, so the large table of attention scores is never written to the slower main memory.
key_features:
- IO-aware tiling
- Online softmax (no full score matrix)
- Fused kernel, fewer HBM reads/writes
- Exact (not approximate) attention
- Extra memory linear in sequence length
paper_title: 'FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness'
paper_url: https://arxiv.org/abs/2205.14135
influenced_by:
- architecture-era/transformer
led_to: []
analogy:
  title: Adding up a ledger on a notepad, not in a filing cabinet
  body: 'This is an analogy, not the mechanism. Picture totting up a huge ledger. The slow way copies every intermediate figure into a giant spreadsheet in a filing cabinet across the room, then walks back to fetch it: the walking, not the adding, takes the time. FlashAttention keeps a few running totals on a notepad at the desk. It reads one small block of the ledger, updates the totals, corrects them when a bigger number turns up, and moves on, never filling the spreadsheet. The cabinet is the GPU''s main memory (HBM), the notepad is its on-chip memory (SRAM), and the running totals are the online softmax of the worked example.'
narrative:
  motivation: '**Attention was slow because of memory traffic, not only arithmetic.** The paper is by **Dao, Fu, Ermon, Rudra and Ré (2022)**.


    - **The cost:** self-attention''s time and memory grow **quadratically** with sequence length.

    - **Earlier fixes:** approximate methods (sparse or low-rank) cut the arithmetic, but the authors note that many show **no wall-clock speedup**, because they ignore the cost of memory access (IO).

    - **The hardware:** an A100 GPU has **40-80 GB** of high-bandwidth memory (HBM) at **1.5-2.0 TB/s**, and **192 KB** of on-chip SRAM in each of its **108** streaming multiprocessors at about **19 TB/s**.'
  approach: '**Make attention IO-aware.**


    - **The principle:** count reads and writes between the levels of GPU memory, not only floating-point operations.

    - **The goal:** never read or write the N × N attention matrix to and from HBM.

    - **Two established techniques:** **tiling** (compute softmax block by block, with extra statistics) and **recomputation** (rebuild the attention matrix in the backward pass instead of storing it).'
  solution: '**One fused kernel that walks through blocks.**


    - **Tiling:** queries, keys and values are split into blocks; for each block of keys and values, every block of queries passes through SRAM, and a running maximum **m** and running sum **ℓ** keep the softmax exact.

    - **Fusion:** matrix multiply, softmax, optional masking and dropout, and the second matrix multiply run in **one CUDA kernel**.

    - **Recomputation:** the backward pass rebuilds the attention matrix in SRAM from the stored output and (m, ℓ); the authors report it is faster despite the extra arithmetic.

    - **IO cost:** **Θ(N²d²/M)** HBM accesses for SRAM size M, against **Θ(Nd + N²)** for standard attention; up to **9×** fewer in the authors'' measurements.'
  outcome: '**Results the authors report:**


    - **Training speed:** BERT-large **15%** faster than the MLPerf 1.1 record, GPT-2 up to **3×** faster than the Hugging Face implementation, and the Long Range Arena **2.4×** faster.

    - **Longer context:** GPT-2 with a **4K** context still trains **30%** faster than Megatron-LM at 1K, with **0.7** better perplexity; the first Transformer above chance on **Path-X** (length **16K**, **61.4%**).

    - **Adoption:** the LLaMA training code used an efficient attention from the xformers library that uses the backward pass of **Dao et al. (2022)**.


    **What came next:**


    - **FlashAttention-2 (Dao, 2023):** better work partitioning, about **2×** faster, **50-73%** of the A100''s peak.

    - **FlashAttention-3 (Shah et al., 2024):** uses the asynchrony and FP8 support of Hopper GPUs; **1.5-2.0×** faster on H100, up to **740 TFLOPs/s** in FP16.'
key_equation:
  latex: \begin{aligned} m^{(j)} &= \max\!\left(m^{(j-1)},\ \mathrm{rowmax}\,S^{(j)}\right) \\ \ell^{(j)} &= e^{m^{(j-1)}-m^{(j)}}\,\ell^{(j-1)} + \mathrm{rowsum}\,e^{S^{(j)}-m^{(j)}} \\ \tilde{O}^{(j)} &= e^{m^{(j-1)}-m^{(j)}}\,\tilde{O}^{(j-1)} + e^{S^{(j)}-m^{(j)}}\,V_j \\ O &= \tilde{O}^{(T_c)} / \ell^{(T_c)} \end{aligned}
  plain: 'In words: after each block j of keys, update the largest score seen so far (m), the running sum of exponentials (ℓ) and the running weighted sum of values (Õ). Whenever the largest score grows, the old ℓ and Õ are multiplied by e^(old max − new max) so every term is measured against the same maximum. After the last block, dividing Õ by ℓ gives exactly softmax(scores) × V. This is the online softmax as the FlashAttention-2 paper writes it (Section 3.1.1); the worked example runs it with two blocks.'
  terms:
  - symbol: S^{(j)}
    meaning: 'the scores of one block of queries against key block j: a small tile of QKᵀ, held in SRAM'
  - symbol: m^{(j)}
    meaning: the largest score in each row seen up to block j; subtracting it keeps every exponential at most 1, so nothing overflows
  - symbol: \ell^{(j)}
    meaning: 'the running sum of e^(score − m) over all keys seen so far: the softmax denominator'
  - symbol: \tilde{O}^{(j)}
    meaning: 'the running sum of e^(score − m) × value: the output before division'
  - symbol: V_j
    meaning: the values of key block j
  - symbol: T_c
    meaning: the number of key blocks; after the last one the output is exact
strengths:
- '**Exact:** the paper proves that the algorithm returns **softmax(QKᵀ)V**; GPT-2 trained with it reaches the **same perplexity** (18.2 for GPT-2 small) as the baselines.'
- '**Memory linear in length:** it needs **O(N)** extra memory beyond inputs and output; the authors measure up to **20×** less memory than exact attention baselines.'
- '**Faster training:** GPT-2 small trains in **2.7 days** on 8 A100 GPUs against **9.5 days** for the Hugging Face implementation, the authors report.'
- '**Longer context:** Path-X (sequence length **16K**) reached **61.4%** accuracy, the first Transformer above chance on it.'
- '**Near the limit for IO:** the authors prove that no exact attention algorithm can asymptotically reduce HBM accesses further **over all SRAM sizes**.'
weaknesses:
- '**Arithmetic still grows with N²:** it performs **O(N²d)** floating-point operations; the authors report that approximate methods become faster for sequences beyond about **512 to 1,024** tokens.'
- '**Far from peak in version 1:** the FlashAttention-2 paper reports that the first version''s forward pass reaches only **30-50%** of the A100''s theoretical maximum, and the backward pass **25-35%**.'
- '**A hand-written kernel per variant:** the authors note that each new attention variant needs a **new CUDA kernel**, with significant engineering effort, and may not transfer across GPU architectures; FlashAttention-3 targets **Hopper** GPUs.'
- '**Single-GPU analysis:** its IO analysis is optimal within constants for **one GPU**; data movement between GPUs is left to future work.'
visual:
  kind: distribution
  tokens:
  - Key 1
  - Key 2
  - Key 3
  - Key 4
  logits:
  - 1
  - 2
  - 3
  - 0
  truncate:
    remove:
    - Key 3
    - Key 4
  panel_titles:
  - 'All 4 keys: the exact softmax FlashAttention ends with'
  - 'Block 1 only: the running weights before block 2 is loaded'
  caption: 'The attention weights of one query over four keys with the example scores 1, 2, 3 and 0, as in the worked example. Left: the exact softmax over all four keys, which FlashAttention returns. Right: after the first block (keys 1 and 2) only, the running result holds the weights 0.269 and 0.731; when block 2 arrives they are rescaled into the left panel''s weights. The scores are an example, not from a trained model.'
comparison:
  before: Standard attention
  after: FlashAttention
  rows:
  - aspect: Is the N × N score matrix written to the GPU's main memory (HBM)?
    before: 'Yes: S = QKᵀ and P = softmax(S) are both written and read back'
    after: 'No: only one small tile exists at a time, inside on-chip SRAM'
    source: https://arxiv.org/abs/2205.14135
  - aspect: Extra memory beyond inputs and output
    before: O(N²)
    after: O(N)
    source: https://arxiv.org/abs/2205.14135
  - aspect: HBM accesses (head size d, SRAM size M)
    before: Θ(Nd + N²)
    after: Θ(N²d²/M)
    source: https://arxiv.org/abs/2205.14135
  - aspect: Arithmetic, GPT-2 medium attention, forward and backward (Figure 2)
    before: 66.6 GFLOPs
    after: 75.2 GFLOPs (more, because of recomputation)
    source: https://arxiv.org/abs/2205.14135
  - aspect: HBM reads and writes, same setting
    before: 40.3 GB
    after: 4.4 GB
    source: https://arxiv.org/abs/2205.14135
  - aspect: Runtime, same setting
    before: 41.7 ms
    after: 7.3 ms
    source: https://arxiv.org/abs/2205.14135
key_numbers:
  columns:
  - Measurement
  - Before
  - After
  - Change
  rows:
  - cells:
    - BERT-large training to 72.0% masked-LM accuracy, 8 A100 GPUs
    - 'Nvidia MLPerf 1.1: 20.0 minutes'
    - 'FlashAttention: 17.4 minutes'
    - 15% faster
    source: https://arxiv.org/abs/2205.14135
  - cells:
    - GPT-2 small training on OpenWebText, 8 A100 GPUs
    - 'Hugging Face: 9.5 days'
    - 'FlashAttention: 2.7 days'
    - 3.5× faster, same perplexity 18.2
    source: https://arxiv.org/abs/2205.14135
  - cells:
    - GPT-2 medium training on OpenWebText, 8 A100 GPUs
    - 'Hugging Face: 21.0 days'
    - 'FlashAttention: 6.9 days'
    - 3.0× faster
    source: https://arxiv.org/abs/2205.14135
  - cells:
    - Path-X (sequence length 16K) accuracy
    - 'Earlier Transformers: out of memory or chance level'
    - 'FlashAttention: 61.4%'
    - first above chance
    source: https://arxiv.org/abs/2205.14135
  - cells:
    - Share of A100 theoretical peak
    - 'FlashAttention: 25-40%'
    - 'FlashAttention-2: 50-73%'
    - about 2× faster
    source: https://arxiv.org/abs/2307.08691
  - cells:
    - Attention on H100
    - 'FlashAttention-2: 35% utilisation'
    - 'FlashAttention-3: up to 740 TFLOPs/s in FP16 (75%)'
    - 1.5-2.0× faster
    source: https://arxiv.org/abs/2407.08608
misunderstandings:
- misunderstanding: FlashAttention approximates attention to make it faster.
  truth: 'It computes **exact** attention: the paper proves that its algorithm returns **softmax(QKᵀ)V**. GPT-2 trained with it reaches the **same perplexity** as with the Hugging Face and Megatron-LM implementations, because the model definition is unchanged. Only the separate **block-sparse** variant in the same paper is approximate.'
  source: https://arxiv.org/abs/2205.14135
- misunderstanding: FlashAttention makes attention's arithmetic linear in sequence length.
  truth: The arithmetic is still **O(N²d)**, and in the authors' GPT-2 medium measurement FlashAttention does **more** of it (**75.2** against **66.6** GFLOPs) because the backward pass recomputes attention. It is faster because HBM traffic falls from **40.3 GB** to **4.4 GB**; it is the memory, not the arithmetic, that becomes linear.
  source: https://arxiv.org/abs/2205.14135
- misunderstanding: FlashAttention is a new model architecture.
  truth: 'It is an algorithm, implemented as a GPU kernel, that computes the **same function** as standard attention. A model needs no change to use it: the authors'' GPT-2 runs keep the model definition and produce the same training and validation curves.'
  source: https://arxiv.org/abs/2205.14135
- misunderstanding: FlashAttention invented computing softmax block by block.
  truth: The paper calls tiling, or softmax scaling, a **well-established technique** and cites earlier work for it, including the online normaliser of **Milakov and Gimelshein (2018)** and the memory-efficient attention of **Rabe and Staats**. Its own contributions are counting memory traffic (IO-awareness), fusing the whole computation into one kernel and a simpler backward pass that recomputes only the attention matrix.
  source: https://arxiv.org/abs/2205.14135
diagram_svg: '<svg class="atlas-figure" viewBox="0 0 860 548" role="img" aria-label="Standard attention writes the N by N matrices S and P to slow GPU memory (HBM); FlashAttention instead loops over blocks of Q, K and V, computes one tile of the N by N score matrix at a time in fast on-chip SRAM with running softmax statistics, and writes back only the output and two length-N statistics, giving the exact same output with memory linear in N.">

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

  '
diagram_caption: 'Standard attention stores the N×N matrices S and P in slow HBM; FlashAttention computes one tile of S at a time in on-chip SRAM with running softmax statistics and writes back only O, ℓ and m: exact output, O(N) extra memory.'
notable_models:
- name: FlashAttention
  year: 2022
  note: Treated attention as an IO problem and computed exact attention with linear extra memory via tiling, an online softmax and recomputation.
- name: FlashAttention-2
  year: 2023
  note: Better parallelism and work partitioning; the author reports about 2× speedup over FlashAttention and 50-73% of theoretical peak FLOPs/s on A100.
- name: FlashAttention-3
  year: 2024
  note: Uses Hopper GPU asynchrony and FP8 low precision; the authors report 1.5-2.0× speedup on H100 and up to 740 TFLOPs/s in FP16.
resources:
- kind: paper
  title: 'FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness (Dao et al., 2022)'
  url: https://arxiv.org/abs/2205.14135
- kind: paper
  title: 'FlashAttention-2: Faster Attention with Better Parallelism and Work Partitioning (Dao, 2023)'
  url: https://arxiv.org/abs/2307.08691
- kind: blog
  title: 'ELI5: FlashAttention (Aleksa Gordić)'
  url: https://gordicaleksa.medium.com/eli5-flash-attention-5c44017022ad
- kind: paper
  title: Online normalizer calculation for softmax (Milakov & Gimelshein, 2018)
  url: https://arxiv.org/abs/1805.02867
- kind: paper
  title: 'FlashAttention-3: Fast and Accurate Attention with Asynchrony and Low-precision (Shah et al., 2024)'
  url: https://arxiv.org/abs/2407.08608
learn_first:
- label: Attention & the softmax it depends on
  id: architecture-era/transformer
  tag: Architecture
- label: Softmax & numerical stability (the max-subtraction trick)
  tag: Math
- label: GPU memory hierarchy — SRAM vs. HBM
  tag: Hardware
tags:
- flashattention
- attention
- gpu
- systems
- long-context
updated: '2026-11-23'
sources:
- url: https://arxiv.org/abs/2205.14135
  title: 'FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness (Dao et al., 2022)'
  trust: 0.95
- url: https://arxiv.org/abs/2307.08691
  title: 'FlashAttention-2: Faster Attention with Better Parallelism and Work Partitioning (Dao, 2023)'
  trust: 0.95
- url: https://arxiv.org/abs/2407.08608
  title: 'FlashAttention-3: Fast and Accurate Attention with Asynchrony and Low-precision (Shah et al., 2024)'
  trust: 0.95
- url: https://arxiv.org/abs/1805.02867
  title: Online normalizer calculation for softmax (Milakov and Gimelshein, 2018)
  trust: 0.9
- url: https://arxiv.org/abs/2302.13971
  title: 'LLaMA: Open and Efficient Foundation Language Models (Touvron et al., 2023)'
  trust: 0.95
---

## What Changed

FlashAttention (Dao et al., 2022) changes **how attention is computed on a GPU**, not what it
computes. The model, its weights and its outputs stay the same.

How to read the table above:

- **N** is the number of tokens and **d** the size of each attention head (64 in GPT-2).
- **HBM** is the GPU's large main memory; **SRAM** is the small memory on the chip itself, about an
  order of magnitude faster.
- **Standard attention** writes two N × N tables to HBM (the scores S and the weights P) and
  reads them back; for N = 1,024 each table has 1,048,576 entries per head.
- **FlashAttention** keeps one small tile of S at a time in SRAM and writes back only the output
  and two numbers per row (m and ℓ).
- **The measured rows** (GPT-2 medium, sequence length 1,024, head size 64, 16 heads, batch 64,
  A100 GPU) show the trade: about **13% more arithmetic** but about **9× fewer** memory reads and
  writes, and a **5.7×** shorter runtime.

## Key Ideas

- **IO-awareness:** count reads and writes between HBM and SRAM, not only floating-point operations.
- **Tiling with an online softmax:** process attention in SRAM-sized blocks and correct a running
  softmax as each block arrives.
- **Kernel fusion:** every step of attention runs in one GPU kernel, so intermediates never reach HBM.
- **Recomputation:** the backward pass rebuilds attention tiles instead of storing the N × N table.
- **Exact, not approximate:** the result equals standard attention.

## How It Works

The figure above has three parts: standard attention (left), FlashAttention's tiles and loops
(middle) and where each tile lives (right). Follow the middle part.

A **token** is a piece of text, a word or part of a word. For one attention head, **Q**, **K** and
**V** hold one query, key and value row per token: N rows of d numbers each.

1. **Inputs in HBM.** Q, K and V start in the GPU's main memory, as for standard attention.
2. **What standard attention does (left).** It computes S = QKᵀ and writes it to HBM, reads it
   back for the softmax and writes P, then reads P and V to compute O = PV. Each table is N × N.
3. **Choose block sizes.** From the SRAM size M, the paper sets the key block to ⌈M / 4d⌉ rows and
   the query block to min(⌈M / 4d⌉, d) rows, so a working set fits on the chip.
4. **Outer loop (top arrow).** Load one block of keys and values, K_j and V_j, into SRAM.
5. **Inner loop (side arrow).** For every block of queries Q_i, load it into SRAM together with
   its running output O_i and its statistics ℓ_i and m_i.
6. **Compute one tile (the highlighted square).** In SRAM: the scores S_ij = Q_i K_jᵀ, their row
   maximum, the exponentials e^(S_ij − max) and their row sums.
7. **Update and write back.** Correct ℓ_i and O_i for the new maximum (the key equation), add this
   tile's contribution, and write O_i, ℓ_i and m_i back to HBM. S_ij and its exponentials are
   discarded, never written.
8. **Finish.** After all blocks, O is exactly softmax(QKᵀ)V. The only extra memory is ℓ and m,
   N numbers each: O(N).
9. **Backward pass (training).** From Q, K, V, the output and (m, ℓ), the tiles of S and P are
   recomputed in SRAM; the authors report this is faster than reading a stored N × N table.
10. **FlashAttention-2.** Swaps the two loops (queries outside, keys inside), keeps the output
    unscaled until the end and divides by ℓ once, and stores the single number m + log ℓ per row
    for the backward pass.

## Worked Example

**One query, four keys, two blocks.** The scores and values are an example, not from a trained
model; they are small so every step can be checked by hand.

| Key | Score (query · key) | Value |
| --- | --- | --- |
| 1 | 1 | (1, 0) |
| 2 | 2 | (0, 1) |
| 3 | 3 | (1, 1) |
| 4 | 0 | (2, 0) |

**The answer to match: ordinary softmax over all four** (left panel of the figure).

1. e¹ ≈ 2.718, e² ≈ 7.389, e³ ≈ 20.086, e⁰ = 1; the total is 31.193.
2. Weights: **0.0871, 0.2369, 0.6439, 0.0321**.
3. Output: 0.0871 × (1, 0) + 0.2369 × (0, 1) + 0.6439 × (1, 1) + 0.0321 × (2, 0)
   ≈ **(0.7952, 0.8808)**.

**FlashAttention, block 1** (keys 1 and 2 only; the right panel):

1. **Largest score so far:** m = max(1, 2) = **2**.
2. **Exponentials against it:** e^(1 − 2) ≈ 0.3679 and e^(2 − 2) = 1.
3. **Running sum:** ℓ ≈ 0.3679 + 1 = **1.3679**.
4. **Running output:** Õ ≈ 0.3679 × (1, 0) + 1 × (0, 1) = **(0.3679, 1)**.
5. **Seen as weights:** 0.3679 / 1.3679 ≈ 0.269 and 1 / 1.3679 ≈ 0.731, the right panel. They are
   correct for these two keys only.

**Block 2** (keys 3 and 4 arrive):

1. **New largest score:** m = max(2, 3) = **3**, so the old totals must be corrected.
2. **Correction factor:** e^(old m − new m) = e^(2 − 3) ≈ **0.3679**.
3. **New exponentials:** e^(3 − 3) = 1 and e^(0 − 3) ≈ 0.0498.
4. **Running sum:** ℓ ≈ 0.3679 × 1.3679 + 1 + 0.0498 ≈ 0.5032 + 1.0498 = **1.5530**.
5. **Running output:**
   - Old part, corrected: 0.3679 × (0.3679, 1) ≈ (0.1353, 0.3679).
   - New part: 1 × (1, 1) + 0.0498 × (2, 0) ≈ (1, 1) + (0.0996, 0).
   - Sum: Õ ≈ **(1.2349, 1.3679)**.
6. **Divide once at the end:** O = Õ / ℓ ≈ (1.2349 / 1.5530, 1.3679 / 1.5530) ≈ **(0.7952, 0.8808)**,
   the same as the ordinary softmax.

**What this means:** the four scores were never held together, yet the result is exact, because
each correction factor re-measures the earlier terms against the new maximum. At full size the same
steps run on tiles of many rows in SRAM, which is why the N × N table never has to be written to
the GPU's main memory.
