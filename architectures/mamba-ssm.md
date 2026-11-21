---
id: architecture-era/mamba-ssm
type: architecture-era
slug: mamba-ssm
schema_version: 1.0
title: "Mamba & Selective State-Space Models"
name: "Mamba (Selective SSM)"
era: post-transformer
era_range: "2023–2026"
year: 2023
family: llm
core_innovation: "An input-dependent (selective) state-space layer that models sequences in linear time with a fixed-size recurrent state — no growing KV-cache."
description: "A subquadratic, recurrent-state alternative to attention with O(1)-per-step inference memory."
key_features: ["Selective (input-dependent) state-space parameters", "Linear-time sequence scaling", "Constant inference state (no KV-cache)", "Hardware-aware parallel scan", "Attention-free backbone"]
key_models: ["Mamba (2023)", "Mamba-2 (2024)", "Jamba (2024)"]
paper_title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces"
paper_url: "https://arxiv.org/abs/2312.00752"
influenced_by: [architecture-era/rnn-lstm, architecture-era/transformer]
led_to: []

eli5: "Instead of re-reading the whole conversation for every new word like attention does, Mamba keeps a small running summary and updates it one word at a time. The clever part: it decides, per word, what's worth keeping in the summary and what to forget — so the summary never grows, no matter how long the text gets."

analogy:
  title: "A skilled note-taker in a long meeting"
  body: "Attention is like recording the entire meeting and re-listening to every second before you speak. Mamba is the sharp assistant who keeps one running page of notes. As each sentence arrives they decide on the spot: is this important enough to write down, does it update something already noted, or can I ignore it? The notes never grow past one page — a fixed-size state — yet they capture what matters across a three-hour meeting. That per-sentence 'decide what to keep' judgment is the *selective* part; the fixed page is the *state*."

narrative:
  motivation: "Attention is brilliant but expensive: its cost grows quadratically with sequence length, and at inference it keeps a key-value (KV) cache that grows with every generated token. For very long sequences — genomes, books, hour-long audio — that quadratic blow-up and ballooning memory become the bottleneck. The old recurrent networks (RNNs/LSTMs) had a fixed-size state and were cheap per step, but they trained slowly and forgot too easily."
  approach: "Revive recurrence, but rebuild it on state-space models (SSMs) — a linear recurrence that can be run as a parallel scan for fast training. Classic SSMs were linear-time but content-agnostic: their dynamics were fixed regardless of input, so they couldn't choose what to remember. Mamba's move is to make the SSM's parameters input-dependent — selective — so the model gates information per token, the very ability that made attention powerful."
  solution: "At each step Mamba updates a fixed-size hidden state from the previous state and the current input, then reads the output from that state — a recurrence. Because the input-selection matrices depend on the current token, the model can write, ignore, or forget content-wise. To keep this fast on GPUs, Mamba never runs the recurrence naively; it uses a hardware-aware parallel scan that computes all states in parallel during training while keeping memory traffic low."
  outcome: "The payoff is linear-time training in sequence length and constant memory per step at inference — a fixed recurrent state instead of a growing KV-cache. Mamba matches or beats similarly-sized transformers on language, audio, and genomics, and shines on very long sequences. Mamba-2 tied SSMs and attention together theoretically, and hybrids like Jamba interleave Mamba and attention layers to get the best of both. It is the most prominent challenger to attention as the sequence-modeling substrate."

build_steps:
  - { title: "1. Represent the sequence as a linear recurrence", detail: "Adopt a state-space model: a small hidden state h carries information forward, updated at each step from the previous state and the current input, with the output read off the state." }
  - { title: "2. Discretize the continuous dynamics", detail: "State-space models are defined in continuous time; a step size turns the continuous matrices into discrete per-token update matrices (Ā, B̄) the model actually applies." }
  - { title: "3. Make the parameters selective", detail: "Let the input-selection matrices (and step size) depend on the current token. Now the model chooses per token what to write into and read from its state — content-based gating, the piece fixed SSMs lacked." }
  - { title: "4. Run it as a parallel scan", detail: "A naive recurrence is sequential and slow. A hardware-aware parallel scan computes all states in parallel during training, keeping the fast on-chip memory busy and slow-memory traffic low." }
  - { title: "5. Wrap it in a gated block", detail: "Surround the selective SSM with input/output projections and a gating branch (a SiLU-gated path), giving the Mamba block that replaces attention in the network." }
  - { title: "6. Stack the blocks", detail: "Stack Mamba blocks like transformer layers. At inference the state stays fixed-size, so generation costs constant memory per token no matter how long the context." }

key_equation:
  latex: "h_t = \\bar{A}\\, h_{t-1} + \\bar{B}\\, x_t, \\qquad y_t = C\\, h_t"
  plain: "Carry a hidden state forward one step at a time: the new state is a decayed copy of the old state (Ā·h) plus the newly written input (B̄·x), and the output is read off the state (C·h). In Mamba, B, C and the step size Δ are computed from the current token, while A is a fixed learned matrix; Ā and B̄ follow from A, B and Δ, so they change with every token. That input-dependence is the selectivity that lets the model decide what to remember."
  terms:
    - { symbol: "h_t", meaning: "the fixed-size hidden state at step t — the running summary" }
    - { symbol: "x_t", meaning: "the input at step t (the current token's vector)" }
    - { symbol: "y_t", meaning: "the output at step t, read from the state" }
    - { symbol: "Ā", meaning: "the discretized state-transition matrix — how much of the previous state to keep (the forget/decay behavior)" }
    - { symbol: "B̄", meaning: "the discretized input matrix — how the current token writes into the state" }
    - { symbol: "C", meaning: "the output matrix — how the state maps to the emitted output; in Mamba B̄ and C depend on the input" }

strengths:
  - "Linear-time in sequence length — far cheaper than attention's quadratic cost on long inputs."
  - "Constant memory per step at inference: a fixed-size state, no KV-cache that grows with context."
  - "Selectivity recovers content-based remembering/forgetting that fixed SSMs and vanilla RNNs lacked."
  - "Strong across modalities — language, audio, and genomics — especially on very long sequences."

weaknesses:
  - "In-context recall and copying can lag attention, since everything must be squeezed into a fixed state."
  - "The hardware-aware parallel scan is a custom kernel, harder to implement and tune than standard attention."
  - "A newer, less battle-tested ecosystem than transformers; many best practices are still being worked out."
  - "Pure SSMs often need attention layers mixed in (hybrids like Jamba) to match transformers on some tasks."

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
  - { name: "Mamba", year: 2023, note: "Introduced the selective SSM and hardware-aware scan; matched transformers at similar sizes with linear-time scaling." }
  - { name: "Mamba-2", year: 2024, note: "The state-space duality — showed SSMs and attention are two views of one framework, and sped up the core layer." }
  - { name: "Jamba", year: 2024, note: "Production hybrid interleaving Mamba and attention layers, proving the design scales to a large, usable long-context model." }
  - { name: "Vision Mamba (Vim)", year: 2024, note: "Carried selective SSMs to images, showing the approach generalizes beyond text." }

resources:
  - { kind: paper, title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)", url: "https://arxiv.org/abs/2312.00752" }
  - { kind: blog, title: "The Annotated S4 (Sasha Rush & Sidd Karamcheti)", url: "https://srush.github.io/annotated-s4/" }
  - { kind: blog, title: "A Visual Guide to Mamba and State Space Models (Maarten Grootendorst)", url: "https://newsletter.maartengrootendorst.com/p/a-visual-guide-to-mamba-and-state" }
  - { kind: paper, title: "Transformers are SSMs: Generalized Models and Efficient Algorithms (Mamba-2, Dao & Gu, 2024)", url: "https://arxiv.org/abs/2405.21060" }

learn_first:
  - { label: "RNNs & LSTMs — recurrence and hidden state", href: "/models/architecture/rnn-lstm", tag: "Architecture" }
  - { label: "The Transformer & why attention is quadratic", href: "/models/architecture/transformer", tag: "Architecture" }
  - { label: "The KV-cache and its memory growth", href: "/learn/concepts/kv-cache", tag: "Concept" }

tags: [mamba, ssm, state-space, linear-time, subquadratic, llm]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2312.00752", title: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces (Gu & Dao, 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2405.21060", title: "Transformers are SSMs (Mamba-2, Dao & Gu, 2024)", trust: 0.9 }
---

## What Changed
The transformer's attention is quadratic in sequence length, and at inference it keeps a key-value (KV)
cache that grows with every generated token. Mamba revives the *recurrent* idea — a fixed-size state carried
through the sequence, in the spirit of the LSTM — but rebuilt on **state-space models (SSMs)** so it
trains efficiently in parallel. Prior SSMs were linear-time but *content-agnostic*: their dynamics were
the same regardless of the input, so they could not selectively remember or forget. Mamba's key move is
to make the SSM parameters **input-dependent (selective)** — the model chooses, per token, what to
write into and read from its state — recovering the content-based reasoning that made attention
powerful. To keep this fast on GPUs it uses a hardware-aware **parallel scan**. The payoff:
**linear-time** training in sequence length and **constant memory per step at inference** (a fixed
recurrent state instead of a growing KV-cache), which is very attractive for extremely long sequences.
It descends conceptually from RNN/LSTM recurrence and is the most prominent challenger to attention as
the sequence-modeling substrate.

## Key Ideas
- **State-space core** — a continuous-style linear recurrence maps inputs to a hidden state to outputs;
  it can be run as an efficient convolution/scan for parallel training.
- **Selectivity** — making the SSM's transition/input matrices depend on the current token lets the
  model gate information content-wise, the ability fixed-parameter SSMs lacked.
- **Linear-time, constant-state** — compute scales linearly with length and inference keeps an
  $O(1)$-size state, avoiding the transformer's growing KV-cache.
- **Recurrence, reborn** — it inherits the RNN's fixed-state efficiency but sidesteps sequential
  training via a hardware-aware parallel scan, making long-context modeling cheap.
