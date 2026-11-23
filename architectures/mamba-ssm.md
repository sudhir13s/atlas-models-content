---
id: architecture-era/mamba-ssm
type: architecture-era
slug: mamba-ssm
schema_version: 2.5
title: "Mamba & Selective State-Space Models"
name: "Mamba (Selective SSM)"
era: post-transformer
era_range: "2023–2026"
year: 2023
family: llm
core_innovation: "An input-dependent (selective) state-space layer that models sequences in linear time with a fixed-size recurrent state, so generation needs no growing key-value cache."
description: "A sequence model with no attention that reads a text one token at a time into a small, fixed-size memory (its state), and lets each token decide how much of that memory to keep and how much of itself to write in."
key_features: ["Selective (input-dependent) state-space parameters", "SSM layers scale linearly with sequence length", "SSM layers keep a fixed-size state instead of a growing key-value cache", "Hardware-aware parallel scan"]
key_models: ["Mamba (2023)", "Mamba-2 (2024)", "Jamba (2024)"]
paper_title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces"
paper_url: "https://arxiv.org/abs/2312.00752"
influenced_by: [architecture-era/rnn-lstm, architecture-era/transformer]
led_to: []

analogy:
  title: "A note-taker with one page"
  body: "This is an analogy, not the mechanism. Attention is like keeping a recording of the whole meeting and replaying all of it before every sentence you say. Mamba is a note-taker with a single page. As each sentence arrives, the note-taker decides how much of the page to keep and how much of the new sentence to write in: an important point overwrites old notes, a filler word like \"um\" is barely written at all. The page never grows, however long the meeting. The page is the state; the per-sentence decision is the selection, which the worked example computes with numbers."

narrative:
  motivation: "**Attention is costly on long sequences, and its cheaper rivals lagged on language.** The paper is by **Albert Gu and Tri Dao (2023)**.\n\n- **The cost of attention:** the authors note that attention cannot model anything outside a finite window and scales **quadratically** with the window length.\n- **The rivals:** linear attention, gated convolutions, recurrent models and **structured state space models (SSMs)** run in subquadratic time but had not performed as well as attention on language.\n- **The SSM line:** **S4 (Gu, Goel and Ré, 2021)** reached the state of the art on every Long Range Arena task, including Path-X at length **16K**, and its authors reported generation **60×** faster in their image and language experiments."
  approach: "**Let the model choose what to remember.**\n\n- **The diagnosis:** the authors identify the key weakness of these models as an inability to do **content-based reasoning**.\n- **Why:** earlier SSMs are **linear time-invariant**: the same parameters (Δ, A, B, C) at every step, whatever the input.\n- **The fix:** make **Δ, B and C functions of the current token**, so the model can selectively propagate or forget information along the sequence."
  solution: "**A selective SSM, computed by a hardware-aware scan, inside one simple block.**\n\n- **No convolution:** input-dependent parameters rule out the convolution that made earlier SSMs fast, so the recurrence is computed with a **parallel scan**.\n- **Hardware-aware:** the parameters are loaded from slow GPU memory (HBM) into fast on-chip memory (SRAM), the state is computed there, and only the outputs are written back; the authors report the IO falls by a factor of **N** (the state size) and the scan runs **20-40×** faster than a standard implementation.\n- **Recomputation:** intermediate states are recomputed in the backward pass instead of stored.\n- **The Mamba block:** the H3 block and the Transformer's MLP block merged into one, with expansion factor **E = 2**; two Mamba blocks match the **12D²** parameters of one attention block plus one MLP block."
  outcome: "**Results the authors report:**\n\n- **Throughput:** **5×** higher generation throughput than Transformers of similar size, since no key-value cache is needed.\n- **Language:** Mamba-3B matches Transformers **twice its size**; on six zero-shot tasks Mamba-2.8B averages **63.3**, against **59.1** for Pythia-2.8B and **61.7** for Pythia-6.9B.\n- **Long context:** on the induction heads task it generalises to sequences of **1M** tokens, **4,000×** longer than in training.\n\n**What came next:**\n\n- **Mamba-2 (Dao and Gu, 2024):** relates SSMs to attention through **state space duality**; its core layer is **2-8×** faster than Mamba's selective SSM.\n- **Jamba (Lieber et al., 2024):** interleaves Transformer and Mamba layers with mixture-of-experts; **12B** active of **52B** total parameters, with strong results up to **256K** tokens of context."

key_equation:
  latex: "\\begin{aligned} h_t &= \\bar{A}_t\\, h_{t-1} + \\bar{B}_t\\, x_t \\\\ y_t &= C_t\\, h_t \\\\ \\bar{A}_t &= e^{\\Delta_t A} \\end{aligned}"
  plain: "In words: the new state is the old state, shrunk by Ā, plus the current input, scaled by B̄; the output is read from the state through C. A is learned and the same for every token, but the step size Δ, B and C are computed from the token, so Ā and B̄ change at every step. A large Δ makes Ā small (forget the past, take in the token); a small Δ makes Ā close to 1 (keep the past, ignore the token). The worked example runs this recurrence by hand for three tokens."
  terms:
    - { symbol: "h_t", meaning: "the state after token t: N numbers per channel (N = 16 in the reference code), the same size at every step" }
    - { symbol: "x_t", meaning: "one channel of the current token's vector" }
    - { symbol: "y_t", meaning: "the output for token t in that channel" }
    - { symbol: "\\Delta_t", meaning: "the step size, computed from the token and made positive by softplus: how strongly this token updates the state" }
    - { symbol: "A", meaning: "a learned matrix (diagonal, with negative entries) that does not depend on the input" }
    - { symbol: "\\bar{A}_t", meaning: "how much of the previous state is kept, e to the power Δ·A (the zero-order hold rule)" }
    - { symbol: "\\bar{B}_t, C_t", meaning: "how the token is written into the state and how the state is read out; both computed from the token" }

strengths:
  - "**Linear in length:** the authors report that computation and memory scale **linearly** with sequence length in training, and that generation takes **constant time per step** because no cache of earlier tokens is needed."
  - "**Faster generation:** **5×** higher generation throughput than Transformers of similar size, the authors report; an untrained Mamba-6.9B would have higher throughput than a **5× smaller** Transformer-1.3B."
  - "**Quality at small scale:** on six zero-shot tasks **Mamba-2.8B** averages **63.3**, above Pythia-2.8B (**59.1**) and Pythia-6.9B (**61.7**), all trained on the same **300B** tokens."
  - "**Content selection:** on the Selective Copying task the full Mamba model scores **99.8%**, against **18.3%** for an S4 layer without gating (the paper's Table 1)."
  - "**Long context:** performance improves on real DNA and audio data up to **million-length** sequences, and induction heads are solved at **1M** tokens."

weaknesses:
  - "**A custom kernel is needed:** selectivity rules out the efficient convolution of earlier SSMs, so speed depends on a hand-built, hardware-aware scan."
  - "**Continuous signals:** the authors note that selection can impede performance on data where time-invariant SSMs excel, such as **audio waveforms**."
  - "**A fixed state must compress:** the authors write that a recurrent model's effectiveness is limited by **how well its state has compressed the context**; attention keeps the whole context instead."
  - "**Tested only at small sizes:** the authors' evaluation stays below the **7B** scale of strong open models and leaves open whether Mamba still compares favourably there."
  - "**An unproven toolkit:** the authors ask whether SSMs share the Transformer's affordances, such as fine-tuning, prompting, in-context learning, instruction tuning and quantization."

comparison:
  before: "Earlier structured SSMs (S4)"
  after: "Mamba (selective SSM, S6)"
  rows:
    - { aspect: "Do the parameters Δ, B and C depend on the input?", before: "No: the same for every token (linear time-invariant)", after: "Yes: computed from each token", source: "https://arxiv.org/abs/2312.00752" }
    - { aspect: "How the layer is computed", before: "As one global convolution in training, as a recurrence in generation", after: "As a recurrence only, by a hardware-aware parallel scan", source: "https://arxiv.org/abs/2312.00752" }
    - { aspect: "Selective Copying accuracy (Table 1)", before: "18.3% (S4, no gate); 56.4% (S4 in the Mamba block)", after: "97.0% (S6, no gate); 99.8% (S6 in the Mamba block)", source: "https://arxiv.org/abs/2312.00752" }
    - { aspect: "Language-model perplexity, about 350M parameters (Table 6, lower is better)", before: "10.56 (Mamba block with real-valued S4)", after: "8.69 (Mamba block with S6)", source: "https://arxiv.org/abs/2312.00752" }
    - { aspect: "Can a token be ignored or the state reset?", before: "No: the dynamics cannot depend on the token", after: "Yes: a small Δ ignores a token, a large Δ resets the state", source: "https://arxiv.org/abs/2312.00752" }

key_numbers:
  columns: ["Released model", "Layers", "Model width (D)", "Inner width (E · D, with E = 2)", "Training data"]
  rows:
    - { cells: ["mamba-130m", "24", "768", "1,536", "300B tokens of the Pile"], source: "https://github.com/state-spaces/mamba" }
    - { cells: ["mamba-370m", "48", "1,024", "2,048", "300B tokens of the Pile"], source: "https://github.com/state-spaces/mamba" }
    - { cells: ["mamba-790m", "48", "1,536", "3,072", "300B tokens of the Pile"], source: "https://github.com/state-spaces/mamba" }
    - { cells: ["mamba-1.4b", "48", "2,048", "4,096", "300B tokens of the Pile"], source: "https://github.com/state-spaces/mamba" }
    - { cells: ["mamba-2.8b", "64", "2,560", "5,120", "300B tokens of the Pile"], source: "https://github.com/state-spaces/mamba" }

misunderstandings:
  - misunderstanding: "Mamba is a faster kind of attention."
    truth: "Mamba has **no attention layers and no MLP blocks**: the authors integrate selective SSMs into a simplified network without either. Each channel is carried through a recurrence with a fixed-size state, and no token is compared with every other token. The later Mamba-2 paper shows mathematical connections between SSMs and variants of attention, but Mamba itself computes none."
    source: "https://arxiv.org/abs/2312.00752"
  - misunderstanding: "A recurrent model has to be trained one step at a time, so it cannot use a GPU well."
    truth: "The selective recurrence is not time-invariant, but the authors observe that it can still be parallelised with a **work-efficient parallel scan**. They fuse the discretisation, the scan and the multiplication by C into one GPU kernel and report it **20-40×** faster than a standard scan, and faster than FlashAttention-2 beyond sequence length **2K**."
    source: "https://arxiv.org/abs/2312.00752"
  - misunderstanding: "Selection is always an improvement over time-invariant SSMs."
    truth: "The authors call this **no free lunch**: selection fixes the weakness of SSMs on discrete data such as text and DNA, but can impede performance on data that time-invariant SSMs excel on. Their audio waveform ablations show this trade-off, because audio is uniformly sampled and very smooth."
    source: "https://arxiv.org/abs/2312.00752"
  - misunderstanding: "Mamba has been shown to beat Transformers at every size."
    truth: "The authors write that their evaluation is **limited to small model sizes**, below strong open models such as Llama, and that it **remains to be assessed** whether Mamba still compares favourably at the 7B scale and beyond. Their language results go up to Mamba-2.8B, trained on 300B tokens."
    source: "https://arxiv.org/abs/2312.00752"

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 556" role="img" aria-label="One Mamba block: after a norm, two linear projections widen the input; one branch runs a causal 1-D convolution, SiLU and the selective state-space model, the other is a SiLU gate; their product is projected back and added to the residual. An inset shows the recurrence whose B, C and step size Δ are computed from each input token.">
  <defs><marker id="mamba-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="mamba-arrow-thin" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="mamba-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <text class="d-label" x="250" y="548" text-anchor="middle">input  x   (L × D)</text>
  <path class="d-line-strong" d="M250 534 V503" marker-end="url(#mamba-arrow)"/>
  <rect class="d-box d-role-embedding" x="194" y="472" width="112" height="28" rx="5"/><text class="d-title" x="250" y="490" text-anchor="middle">Norm</text>
  <path class="d-line-back" d="M250 516 H60 V64 H235" marker-end="url(#mamba-arrow-thin)"/>
  <text class="d-note" x="52" y="300" text-anchor="middle" transform="rotate(-90 52 300)">residual</text>
  <rect class="d-frame" x="96" y="100" width="314" height="358" rx="6"/>
  <text class="d-note" x="106" y="116" text-anchor="start">Mamba block</text>
  <path class="d-line-strong" d="M250 472 V446 M170 446 H330"/>
  <path class="d-line-strong" d="M170 446 V421" marker-end="url(#mamba-arrow)"/>
  <path class="d-line-strong" d="M330 446 V421" marker-end="url(#mamba-arrow)"/>
  <rect class="d-box" x="114" y="382" width="112" height="36" rx="5"/><text class="d-title" x="170" y="397" text-anchor="middle">Linear</text><text class="d-note" x="170" y="411" text-anchor="middle">D → ED</text>
  <rect class="d-box" x="274" y="382" width="112" height="36" rx="5"/><text class="d-title" x="330" y="397" text-anchor="middle">Linear</text><text class="d-note" x="330" y="411" text-anchor="middle">D → ED</text>
  <text class="d-note" x="250" y="404" text-anchor="middle">width ×E</text>
  <text class="d-note" x="250" y="417" text-anchor="middle">(E = 2)</text>
  <path class="d-line-strong" d="M330 382 V363" marker-end="url(#mamba-arrow)"/>
  <rect class="d-box d-role-position" x="274" y="324" width="112" height="36" rx="5"/><text class="d-title" x="330" y="339" text-anchor="middle">Conv1d</text><text class="d-note" x="330" y="353" text-anchor="middle">causal, kernel 4</text>
  <path class="d-line-strong" d="M330 324 V301" marker-end="url(#mamba-arrow)"/>
  <rect class="d-box d-role-feedforward" x="274" y="270" width="112" height="28" rx="5"/><text class="d-title" x="330" y="288" text-anchor="middle">σ  SiLU</text>
  <path class="d-line-strong" d="M330 270 V259" marker-end="url(#mamba-arrow)"/>
  <rect class="d-box d-role-attention" x="274" y="206" width="112" height="50" rx="5"/>
  <text class="d-title" x="330" y="226" text-anchor="middle">Selective SSM</text>
  <text class="d-note" x="330" y="242" text-anchor="middle">S6: B, C, Δ from input</text>
  <path class="d-line-strong" d="M170 382 V301" marker-end="url(#mamba-arrow)"/>
  <rect class="d-box d-role-feedforward" x="114" y="270" width="112" height="28" rx="5"/><text class="d-title" x="170" y="288" text-anchor="middle">σ  SiLU</text>
  <text class="d-note" x="162" y="344" text-anchor="end">gate</text>
  <path class="d-line-strong" d="M330 206 V172 H265" marker-end="url(#mamba-arrow)"/>
  <path class="d-line-strong" d="M170 270 V172 H235" marker-end="url(#mamba-arrow)"/>
  <circle class="d-node" cx="250" cy="172" r="12"/>
  <text class="d-label" x="250" y="176" text-anchor="middle">×</text>
  <path class="d-line-strong" d="M250 160 V151" marker-end="url(#mamba-arrow)"/>
  <rect class="d-box" x="194" y="112" width="112" height="36" rx="5"/><text class="d-title" x="250" y="127" text-anchor="middle">Linear</text><text class="d-note" x="250" y="141" text-anchor="middle">ED → D</text>
  <path class="d-line-strong" d="M250 112 V79" marker-end="url(#mamba-arrow)"/>
  <circle class="d-node" cx="250" cy="64" r="12"/>
  <text class="d-label" x="250" y="68" text-anchor="middle">+</text>
  <path class="d-line-strong" d="M250 52 V21" marker-end="url(#mamba-arrow)"/>
  <text class="d-label" x="262" y="30" text-anchor="start">output   (L × D)</text>
  <path class="d-line-accent" stroke-dasharray="4 3" d="M386 231 H468"/>
  <rect class="d-frame" x="468" y="20" width="376" height="526" rx="6"/>
  <text class="d-title" x="484" y="42" text-anchor="start">Inside the selective SSM (one channel)</text>
  <rect class="d-box d-role-router" x="484" y="56" width="344" height="66" rx="5"/>
  <text class="d-math" x="656" y="76" text-anchor="middle">B<tspan dy="3">t</tspan><tspan dy="-3"> = Linear(x</tspan><tspan dy="3">t</tspan><tspan dy="-3">),   C</tspan><tspan dy="3">t</tspan><tspan dy="-3"> = Linear(x</tspan><tspan dy="3">t</tspan><tspan dy="-3">),   Δ</tspan><tspan dy="3">t</tspan><tspan dy="-3"> = softplus(Linear(x</tspan><tspan dy="3">t</tspan><tspan dy="-3">))</tspan></text>
  <text class="d-math" x="656" y="94" text-anchor="middle">Ā<tspan dy="3">t</tspan><tspan dy="-3"> = exp(Δ</tspan><tspan dy="3">t</tspan><tspan dy="-3">A),   B̄</tspan><tspan dy="3">t</tspan><tspan dy="-3"> ≈ Δ</tspan><tspan dy="3">t</tspan><tspan dy="-3">B</tspan><tspan dy="3">t</tspan><tspan dy="-3">   (A learned, fixed)</tspan></text>
  <text class="d-note" x="656" y="112" text-anchor="middle">selection: the parameters are functions of the token</text>
  <path class="d-line-strong" d="M480 250 H515" marker-end="url(#mamba-arrow)"/>
  <path class="d-line-strong" d="M794 250 H818" marker-end="url(#mamba-arrow)"/>
  <circle class="d-node d-role-attention" cx="538" cy="250" r="20"/>
  <text class="d-math" x="538" y="253" text-anchor="middle">h<tspan dy="3">t−1</tspan><tspan dy="-3"></tspan></text>
  <circle class="d-node d-role-input" cx="538" cy="330" r="15"/>
  <text class="d-math" x="538" y="333" text-anchor="middle">x<tspan dy="3">t−1</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-strong" d="M538 315 V273" marker-end="url(#mamba-arrow)"/>
  <text class="d-math" x="530" y="294" text-anchor="end">B̄<tspan dy="3">t−1</tspan><tspan dy="-3"></tspan></text>
  <circle class="d-node d-role-output" cx="538" cy="172" r="15"/>
  <text class="d-math" x="538" y="175" text-anchor="middle">y<tspan dy="3">t−1</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-strong" d="M538 230 V190" marker-end="url(#mamba-arrow)"/>
  <text class="d-math" x="530" y="215" text-anchor="end">C<tspan dy="3">t−1</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-strong" d="M558 250 H633" marker-end="url(#mamba-arrow)"/>
  <text class="d-math" x="597" y="242" text-anchor="middle">Ā<tspan dy="3">t</tspan><tspan dy="-3"></tspan></text>
  <circle class="d-node d-role-attention" cx="656" cy="250" r="20"/>
  <text class="d-math" x="656" y="253" text-anchor="middle">h<tspan dy="3">t</tspan><tspan dy="-3"></tspan></text>
  <circle class="d-node d-role-input" cx="656" cy="330" r="15"/>
  <text class="d-math" x="656" y="333" text-anchor="middle">x<tspan dy="3">t</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-strong" d="M656 315 V273" marker-end="url(#mamba-arrow)"/>
  <text class="d-math" x="648" y="294" text-anchor="end">B̄<tspan dy="3">t</tspan><tspan dy="-3"></tspan></text>
  <circle class="d-node d-role-output" cx="656" cy="172" r="15"/>
  <text class="d-math" x="656" y="175" text-anchor="middle">y<tspan dy="3">t</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-strong" d="M656 230 V190" marker-end="url(#mamba-arrow)"/>
  <text class="d-math" x="648" y="215" text-anchor="end">C<tspan dy="3">t</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-strong" d="M676 250 H751" marker-end="url(#mamba-arrow)"/>
  <text class="d-math" x="715" y="242" text-anchor="middle">Ā<tspan dy="3">t+1</tspan><tspan dy="-3"></tspan></text>
  <circle class="d-node d-role-attention" cx="774" cy="250" r="20"/>
  <text class="d-math" x="774" y="253" text-anchor="middle">h<tspan dy="3">t+1</tspan><tspan dy="-3"></tspan></text>
  <circle class="d-node d-role-input" cx="774" cy="330" r="15"/>
  <text class="d-math" x="774" y="333" text-anchor="middle">x<tspan dy="3">t+1</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-strong" d="M774 315 V273" marker-end="url(#mamba-arrow)"/>
  <text class="d-math" x="766" y="294" text-anchor="end">B̄<tspan dy="3">t+1</tspan><tspan dy="-3"></tspan></text>
  <circle class="d-node d-role-output" cx="774" cy="172" r="15"/>
  <text class="d-math" x="774" y="175" text-anchor="middle">y<tspan dy="3">t+1</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-strong" d="M774 230 V190" marker-end="url(#mamba-arrow)"/>
  <text class="d-math" x="766" y="215" text-anchor="end">C<tspan dy="3">t+1</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-accent" stroke-dasharray="5 4" d="M656 345 V364 H836 V89 H832" marker-end="url(#mamba-arrow-accent)"/>
  <text class="d-math" x="598" y="394" text-anchor="middle">h<tspan dy="3">t</tspan><tspan dy="-3"> = Ā</tspan><tspan dy="3">t</tspan><tspan dy="-3"> h</tspan><tspan dy="3">t−1</tspan><tspan dy="-3"> + B̄</tspan><tspan dy="3">t</tspan><tspan dy="-3"> x</tspan><tspan dy="3">t</tspan><tspan dy="-3"></tspan></text>
  <text class="d-math" x="748" y="394" text-anchor="middle">y<tspan dy="3">t</tspan><tspan dy="-3"> = C</tspan><tspan dy="3">t</tspan><tspan dy="-3"> h</tspan><tspan dy="3">t</tspan><tspan dy="-3"></tspan></text>
  <text class="d-label" x="484" y="424" text-anchor="start">S4 (time-invariant): A, B, C, Δ identical at every step;</text>
  <text class="d-note" x="484" y="438" text-anchor="start">it cannot choose what to keep, but runs as one convolution.</text>
  <text class="d-label" x="484" y="462" text-anchor="start">Mamba (selective): B, C, Δ change with each token;</text>
  <text class="d-note" x="484" y="476" text-anchor="start">it keeps or ignores content, computed by a parallel scan.</text>
  <text class="d-label" x="484" y="504" text-anchor="start">Cost grows linearly with sequence length L;</text>
  <text class="d-note" x="484" y="518" text-anchor="start">the state is a fixed size (N = 16 per channel): no growing cache.</text>
  </svg>
diagram_caption: "A Mamba block: widen, causal conv, SiLU, then the selective SSM, gated by a SiLU branch, projected back and added to the residual. Inset: the recurrence whose B, C and Δ are computed from each token, at linear cost in length."
notable_models:
  - { name: "S4", year: 2021, note: "The structured state space model Mamba builds on: time-invariant, computed as a convolution; state of the art on every Long Range Arena task, including Path-X at length 16K." }
  - { name: "Mamba", year: 2023, note: "Introduced the selective SSM and hardware-aware scan; the authors report Mamba-3B outperforming Transformers of the same size and matching Transformers twice its size." }
  - { name: "Mamba-2", year: 2024, note: "State space duality connects SSMs and variants of attention; its core layer is reported 2-8× faster than Mamba's selective SSM." }
  - { name: "Jamba", year: 2024, note: "A hybrid that interleaves Transformer and Mamba layers, with mixture-of-experts in some layers; 12B active of 52B total parameters, with strong results reported up to 256K tokens of context." }
  - { name: "Vision Mamba (Vim)", year: 2024, note: "A vision backbone of bidirectional Mamba blocks; its authors report that it outperforms DeiT on ImageNet classification and is 2.8× faster while saving 86.8% of GPU memory at 1248 × 1248 resolution." }

resources:
  - { kind: paper, title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)", url: "https://arxiv.org/abs/2312.00752" }
  - { kind: blog, title: "The Annotated S4 (Sasha Rush & Sidd Karamcheti)", url: "https://srush.github.io/annotated-s4/" }
  - { kind: blog, title: "A Visual Guide to Mamba and State Space Models (Maarten Grootendorst)", url: "https://newsletter.maartengrootendorst.com/p/a-visual-guide-to-mamba-and-state" }
  - { kind: paper, title: "Transformers are SSMs: Generalized Models and Efficient Algorithms (Mamba-2, Dao & Gu, 2024)", url: "https://arxiv.org/abs/2405.21060" }
  - { kind: paper, title: "Efficiently Modeling Long Sequences with Structured State Spaces (S4, Gu, Goel & Ré, 2021)", url: "https://arxiv.org/abs/2111.00396" }

learn_first:
  - { label: "RNNs & LSTMs — recurrence and hidden state", href: "/models/architecture/rnn-lstm", tag: "Architecture" }
  - { label: "The Transformer & why attention is quadratic", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "The KV-cache and its memory growth", href: "/learn/concepts/kv-cache", tag: "Concept" }

tags: [mamba, ssm, state-space, linear-time, subquadratic, llm]
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/2312.00752", title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)", trust: 0.95 }
  - { url: "https://github.com/state-spaces/mamba/blob/main/mamba_ssm/modules/mamba_simple.py", title: "Mamba reference implementation (state-spaces/mamba): default state size 16, convolution width 4, expansion 2", trust: 0.9 }
  - { url: "https://github.com/state-spaces/mamba/blob/main/mamba_ssm/ops/selective_scan_interface.py", title: "Mamba reference selective scan (state-spaces/mamba): discretisation and recurrence as computed in code", trust: 0.9 }
  - { url: "https://github.com/state-spaces/mamba", title: "state-spaces/mamba README: released model sizes and training data", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2405.21060", title: "Transformers are SSMs (Mamba-2, Dao & Gu, 2024)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2111.00396", title: "Efficiently Modeling Long Sequences with Structured State Spaces (S4, Gu et al., 2021)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2403.19887", title: "Jamba: A Hybrid Transformer-Mamba Language Model (Lieber et al., 2024)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2401.09417", title: "Vision Mamba: Efficient Visual Representation Learning with Bidirectional State Space Model (Zhu et al., 2024)", trust: 0.95 }
---

## What Changed

Mamba (Gu and Dao, 2023) builds on **structured state space models (SSMs)** such as S4, which
carry a fixed-size state through the sequence. It changes one thing: the parameters that decide
what enters and leaves the state now **depend on the current token**.

How to read the table above:

- **Linear time-invariant (LTI)** means the same Δ, A, B and C at every step. Such a model can be
  run as one long convolution, which is fast, but it treats every token alike.
- **Selective (S6)** means Δ, B and C are computed from each token. The authors call these models
  S6: S4 with a selection mechanism, computed with a scan.
- **Selective Copying** asks a model to remember a few marked tokens scattered among filler
  tokens at random spacing; it needs content, not just timing.
- **Perplexity** measures how surprised a language model is by held-out text; lower is better.
- **Against attention:** a Transformer keeps every earlier token in its key-value cache, so memory
  grows with the text; Mamba keeps a state of fixed size, so each new token costs the same.

## Key Ideas

- **State-space core:** a linear recurrence maps each input channel to a small state and back.
- **Selection:** Δ, B and C come from the token, so the model can keep, ignore or reset.
- **Hardware-aware scan:** kernel fusion, a parallel scan and recomputation replace the convolution
  that selection rules out.
- **One block, stacked:** a gated block with no attention and no MLP, repeated with normalisation
  and residual connections.

## How It Works

The figure above is one Mamba block (left) and the recurrence inside its selective SSM (right).
Follow the block from the bottom.

A **token** is a piece of text, a word or part of a word. Its **vector** is a list of D numbers;
each position in that list is a **channel**.

1. **Input and Norm.** The sequence arrives as L tokens of D numbers each (L × D) and is
   normalised. A copy goes around the block on the residual path.
2. **Two linear projections (D → ED).** Each widens the vector by the expansion factor **E = 2**.
   - **Right branch:** the main path through the SSM.
   - **Left branch:** the gate.
3. **Conv1d (causal, kernel 4).** A short convolution mixes each channel with the 3 tokens before
   it, never with later ones. The kernel width 4 is the reference code's default.
4. **SiLU.** The activation z · σ(z), where σ is the sigmoid.
5. **Selective SSM.** For every token, three small projections of its vector give **B**, **C**
   and the step size **Δ** (made positive by softplus). Then, separately for each channel:
   - **Discretise:** Ā = e^(Δ·A), where A is learned and fixed.
   - **Update the state:** h_t = Ā·h_(t−1) + B̄·x_t, where the state h holds **N = 16** numbers per
     channel in the reference code.
   - **Read out:** y_t = C·h_t. The reference code also adds a skip term D·x_t.
   - **On the GPU:** this runs as a parallel scan inside fast on-chip memory, and only y is
     written back to the GPU's main memory.
6. **Gate (×).** The SSM output is multiplied, number by number, by the SiLU of the left branch.
7. **Linear (ED → D) and "+".** The result is projected back to D numbers and added to the
   residual copy from step 1.
8. **Repeat.** Blocks are stacked, each with its own norm and residual connection: **24** blocks
   in mamba-130m, **64** in mamba-2.8b.
9. **Generating text.** Each new token updates every block's state once. The state stays the same
   size, so the cost per token does not grow with the length of the text.

**What the inset adds:** in S4 the arrows Ā, B̄ and C are the same at every step; in Mamba they are
recomputed from each token, which is what lets the model choose what to keep.

## Worked Example

**Three tokens through one channel of a selective SSM.** The numbers are an example, not from a
trained model. The setting is the paper's Theorem 1, a state of one number (N = 1) with A = −1 and
B = 1, plus C = 1 (our choice) so that y_t = h_t.

**The rule in this setting** (zero-order hold, the paper's equation 4):

- **Keep:** Ā = e^(−Δ).
- **Write:** B̄ = 1 − e^(−Δ).
- **Update:** h_t = Ā · h_(t−1) + B̄ · x_t, starting from h_0 = 0.

Each token arrives with its input x and the step size Δ that its own projection produced:

| Token | Input x | Step size Δ | Role in the example |
| --- | --- | --- | --- |
| 1 | 4 | 2.0 | important: write it in |
| 2 | 1 | 0.05 | filler, like "um": ignore it |
| 3 | −2 | 1.0 | half-and-half |

1. **Token 1** (Δ = 2.0):
   - Ā = e^(−2) ≈ 0.1353 and B̄ = 1 − 0.1353 = 0.8647.
   - h_1 = 0.1353 × 0 + 0.8647 × 4 ≈ **3.459**.
2. **Token 2** (Δ = 0.05):
   - Ā = e^(−0.05) ≈ 0.9512 and B̄ ≈ 0.0488.
   - Kept: 0.9512 × 3.459 ≈ 3.290. Written: 0.0488 × 1 ≈ 0.049.
   - h_2 ≈ 3.290 + 0.049 = **3.339**: the filler barely changed the state.
3. **Token 3** (Δ = 1.0):
   - Ā = e^(−1) ≈ 0.3679 and B̄ ≈ 0.6321.
   - Kept: 0.3679 × 3.339 ≈ 1.228. Written: 0.6321 × (−2) ≈ −1.264.
   - h_3 ≈ 1.228 − 1.264 = **−0.036**.

**The same tokens without selection** (time-invariant, like S4): one fixed Δ = 0.5 for every
token, so Ā = e^(−0.5) ≈ 0.6065 and B̄ ≈ 0.3935 at every step.

1. h_1 = 0.3935 × 4 ≈ **1.574**.
2. h_2 = 0.6065 × 1.574 + 0.3935 × 1 ≈ 0.955 + 0.393 = **1.348**: the filler now writes 0.39 of
   itself into the state, against 0.05 with selection.
3. h_3 = 0.6065 × 1.348 + 0.3935 × (−2) ≈ 0.818 − 0.787 = **0.031**.

**Two checks:**

- **The gate view:** B̄ = 1 − e^(−Δ) is the gate g of Theorem 1, so h_t = (1 − g)·h_(t−1) + g·x_t,
  the update of a gated recurrent network. For Δ = 2 the gate is 0.8647.
- **The reference code** writes B̄·x as Δ·B·x, a simpler rule. For a small Δ the two nearly agree:
  for Δ = 0.05 it gives 0.05 where the formula above gives 0.0488.

**What this means:** with selection, each token sets its own Δ, so an important token can
overwrite the state and a filler token can pass almost unseen. Without selection every token is
written in by the same amount. In both cases the state stays one number per channel (16 in the
real model), however long the text grows.
