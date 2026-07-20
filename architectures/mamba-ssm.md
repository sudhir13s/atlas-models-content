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
  plain: "Carry a hidden state forward one step at a time: the new state is a decayed copy of the old state (Ā·h) plus the newly written input (B̄·x), and the output is read off the state (C·h). In Mamba, Ā, B̄, and C are computed from the current token — that input-dependence is the selectivity that lets the model decide what to remember."
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

diagram: |
  flowchart LR
    X1["x1"] --> H1["state h1"]
    H1 --> Y1["y1"]
    X2["x2"] --> H2["state h2"]
    H1 -->|"keep / forget"| H2
    H2 --> Y2["y2"]
    X3["x3"] --> H3["state h3"]
    H2 -->|"keep / forget"| H3
    H3 --> Y3["y3"]
    SEL["Selection (input-dependent)"] -.->|"controls keep/forget"| H2
    SEL -.->|"controls keep/forget"| H3
diagram_caption: "A fixed-size state is carried forward one token at a time; the input-dependent selection decides how much of the past to keep versus overwrite. The state never grows, so inference memory stays constant."

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
