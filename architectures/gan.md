---
id: architecture-era/gan
type: architecture-era
slug: gan
schema_version: 1.0
title: "Generative Adversarial Networks"
name: "Generative Adversarial Network"
era: foundation
era_range: "2014–2020"
year: 2014
family: generation
core_innovation: "Train a generator against a discriminator in a minimax game, learning to synthesize data with no explicit likelihood."
description: "The adversarial generator-vs-discriminator framework that drove a decade of photorealistic image synthesis."
key_features: ["Generator vs. discriminator minimax game", "Implicit (likelihood-free) generative model", "Sharp, high-fidelity samples", "Adversarial training dynamics"]
key_models: ["DCGAN (2015)", "StyleGAN (2019)", "BigGAN (2018)"]
paper_title: "Generative Adversarial Nets (Goodfellow et al., 2014)"
paper_url: "https://arxiv.org/abs/1406.2661"
influenced_by: []
led_to: [architecture-era/diffusion]

eli5: "A GAN is two networks locked in a contest. One paints fake pictures; the other is a judge trying to catch the fakes. Every round the forger gets sneakier and the judge gets sharper. When the judge can no longer tell fake from real, the forger has learned to make convincing images."

analogy:
  title: "A forger and a detective who train each other"
  body: "Picture an art forger and a detective. The forger paints fakes; the detective examines a mix of real paintings and forgeries and calls each one 'real' or 'fake'. At first both are terrible — the fakes are crude, the detective guesses. But they learn from each other: every time the detective spots a fake, the forger studies why it got caught and improves; every time a forgery slips through, the detective sharpens its eye. Push this contest to its limit and the forger paints works the detective can only guess at — a coin flip. At that point the forgeries are, statistically, indistinguishable from real art. The forger is the generator; the detective is the discriminator; the coin-flip stalemate is the equilibrium GANs chase."

narrative:
  motivation: "By 2014, deep networks were excellent at recognizing images but clumsy at creating them. The leading generative models optimized an explicit likelihood — they tried to write down the probability of every possible image — and that math forced compromises that produced soft, blurry samples. The field wanted a model that could generate a crisp, realistic image, but computing or approximating the true data density was the bottleneck standing in the way."
  approach: "Goodfellow's idea was to sidestep the density entirely and turn generation into a game. Instead of asking a model 'how likely is this image?', pit two networks against each other. A generator invents images from random noise. A discriminator, shown a mix of real and generated images, learns to tell them apart. Train them together in opposition and let competition, not an explicit likelihood, drive the learning."
  solution: "The two networks optimize opposing goals — the discriminator maximizes its accuracy at spotting fakes while the generator minimizes it, a single minimax value function. The generator never sees real data directly; it learns purely from the gradient the discriminator hands back, an answer to 'which way should I nudge this pixel to look more real?'. Because there is no likelihood to compute, sampling is a single fast forward pass through the generator. Goodfellow proved that at the game's equilibrium the generator's distribution exactly matches the real data."
  outcome: "GANs produced strikingly sharp images and dominated synthesis for years — DCGAN made training stable enough to be practical, BigGAN scaled it to high-resolution ImageNet, and StyleGAN generated photorealistic human faces that fooled people. But the adversarial game is delicate: training can oscillate or diverge, and the generator can collapse to producing only a few varieties (mode collapse). Those exact pain points — instability and poor coverage — are what made the calmer, coverage-friendly diffusion models so appealing as successors."

build_steps:
  - { title: "1. Sample random noise", detail: "Draw a vector of random numbers (the latent code). This is the generator's raw material — its source of variety. Different noise vectors will map to different generated images." }
  - { title: "2. Generate a fake with G", detail: "Feed the noise through the generator network, which upsamples it into a full synthetic image. Early in training this looks like garbage; that is expected." }
  - { title: "3. Judge real and fake with D", detail: "Show the discriminator a batch of real images and a batch of generated ones. It outputs, for each, a probability that the image is real." }
  - { title: "4. Train the discriminator", detail: "Update D to push its score toward 1 on real images and toward 0 on fakes — it is learning to be a better detective." }
  - { title: "5. Train the generator", detail: "Update G so the discriminator scores its fakes closer to 1. G learns only from D's gradient — 'which pixel change would fool the judge more?'. It never touches real data." }
  - { title: "6. Alternate to equilibrium", detail: "Repeat steps 1–5, letting each network improve against the other. The target is a stalemate where D is stuck guessing (score ≈ 0.5) because the fakes are indistinguishable from real." }

key_equation:
  latex: "\\min_{G}\\max_{D}\\; \\mathbb{E}_{x\\sim p_{\\text{data}}}[\\log D(x)] + \\mathbb{E}_{z\\sim p_{z}}[\\log(1 - D(G(z)))]"
  plain: "Two players share one score. The discriminator D wants to push this value UP: give high scores to real images and low scores to the generator's fakes. The generator G wants to push it DOWN: make its fakes G(z) look so real that D scores them high. Solving this max-then-min tug-of-war lands at the point where the generator's images match the real data distribution."
  terms:
    - { symbol: "G", meaning: "generator — turns a noise vector z into a fake sample G(z)" }
    - { symbol: "D", meaning: "discriminator — outputs the probability that its input is a real image" }
    - { symbol: "x ~ p_data", meaning: "a real sample drawn from the true data distribution" }
    - { symbol: "z ~ p_z", meaning: "a random noise vector drawn from a simple prior (e.g. a Gaussian)" }
    - { symbol: "D(G(z))", meaning: "the discriminator's score on a generated sample — the generator wants this near 1" }

strengths:
  - "Produces sharp, high-frequency detail — crisper images than the likelihood-based methods of its era."
  - "Sampling is a single fast forward pass through the generator, so inference is quick."
  - "No explicit likelihood needed — the model learns any distribution the discriminator can critique, even ones with no tractable density."
  - "Highly controllable variants (StyleGAN) expose interpretable knobs for style, pose, and identity."

weaknesses:
  - "Training is unstable — the two-player game can oscillate or diverge and is sensitive to hyperparameters."
  - "Mode collapse — the generator can cover only a few varieties of the data instead of the full distribution."
  - "No likelihood means no clean way to measure how well the model fits the data or to compare models."
  - "Requires careful tricks (spectral normalization, gradient penalties) to train reliably at scale."

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 470" role="img" aria-label="A generative adversarial network: noise z goes through the generator G to a fake sample, a real sample comes from the data, and the discriminator D outputs the probability that its input is real; D is trained to tell real from fake while G is trained, through D, to fool it.">
  <defs><marker id="gan-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="gan-arrow-thin" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="gan-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <rect class="d-box d-role-router" x="30" y="96" width="96" height="40" rx="5"/>
  <text class="d-math" x="78" y="120" text-anchor="middle">z ~ p<tspan dy="3">z</tspan><tspan dy="-3"></tspan></text>
  <text class="d-note" x="78" y="152" text-anchor="middle">random noise</text>
  <path class="d-line-strong" d="M126 116 H166" marker-end="url(#gan-arrow)"/>
  <polygon class="d-box d-role-feedforward" points="170,90 310,56 310,176 170,142"/>
  <text class="d-title" x="240" y="113" text-anchor="middle">Generator G</text>
  <text class="d-note" x="240" y="128" text-anchor="middle">noise → sample</text>
  <path class="d-line-strong" d="M310 116 H352" marker-end="url(#gan-arrow)"/>
  <rect class="d-fill-soft" x="358" y="87" width="58" height="58" rx="3"/><rect class="d-box d-role-output" x="372" y="91" width="10" height="10"/><rect class="d-box d-role-output" x="382" y="91" width="10" height="10"/><rect class="d-box d-role-output" x="362" y="101" width="10" height="10"/><rect class="d-box d-role-output" x="392" y="101" width="10" height="10"/><rect class="d-box d-role-output" x="402" y="101" width="10" height="10"/><rect class="d-box d-role-output" x="362" y="111" width="10" height="10"/><rect class="d-box d-role-output" x="402" y="111" width="10" height="10"/><rect class="d-box d-role-output" x="402" y="121" width="10" height="10"/><rect class="d-box d-role-output" x="372" y="131" width="10" height="10"/><rect class="d-box d-role-output" x="382" y="131" width="10" height="10"/><rect class="d-box d-role-output" x="392" y="131" width="10" height="10"/>
  <text class="d-math" x="387" y="162" text-anchor="middle">fake G(z)</text>
  <rect class="d-box d-role-input" x="30" y="284" width="126" height="40" rx="5"/>
  <text class="d-title" x="93" y="302" text-anchor="middle">Training data</text>
  <text class="d-math" x="93" y="317" text-anchor="middle">x ~ p<tspan dy="3">data</tspan><tspan dy="-3"></tspan></text>
  <path class="d-line-strong" d="M156 304 H352" marker-end="url(#gan-arrow)"/>
  <rect class="d-fill-soft" x="358" y="275" width="58" height="58" rx="3"/><rect class="d-box d-role-output" x="372" y="279" width="10" height="10"/><rect class="d-box d-role-output" x="382" y="279" width="10" height="10"/><rect class="d-box d-role-output" x="392" y="279" width="10" height="10"/><rect class="d-box d-role-output" x="362" y="289" width="10" height="10"/><rect class="d-box d-role-output" x="402" y="289" width="10" height="10"/><rect class="d-box d-role-output" x="362" y="299" width="10" height="10"/><rect class="d-box d-role-output" x="402" y="299" width="10" height="10"/><rect class="d-box d-role-output" x="362" y="309" width="10" height="10"/><rect class="d-box d-role-output" x="402" y="309" width="10" height="10"/><rect class="d-box d-role-output" x="372" y="319" width="10" height="10"/><rect class="d-box d-role-output" x="382" y="319" width="10" height="10"/><rect class="d-box d-role-output" x="392" y="319" width="10" height="10"/>
  <text class="d-math" x="387" y="350" text-anchor="middle">real x</text>
  <path class="d-line-strong" d="M420 116 L476 178" marker-end="url(#gan-arrow)"/>
  <path class="d-line-strong" d="M420 304 L476 242" marker-end="url(#gan-arrow)"/>
  <polygon class="d-box d-role-attention" points="480,130 630,172 630,248 480,290"/>
  <text class="d-title" x="552" y="207" text-anchor="middle">Discriminator D</text>
  <text class="d-note" x="552" y="222" text-anchor="middle">sample → score</text>
  <path class="d-line-strong" d="M630 210 H681" marker-end="url(#gan-arrow)"/>
  <circle class="d-node d-role-output" cx="708" cy="210" r="24"/>
  <text class="d-math" x="708" y="214" text-anchor="middle">D(·)</text>
  <text class="d-note" x="742" y="206" text-anchor="start">probability</text>
  <text class="d-note" x="742" y="219" text-anchor="start">input is real</text>
  <path class="d-line-back" d="M708 234 V372 H555 V276" marker-end="url(#gan-arrow-thin)"/>
  <text class="d-title" x="640" y="392" text-anchor="middle">Train D: maximise the objective</text>
  <text class="d-note" x="640" y="407" text-anchor="middle">push D(x) → 1 on real, D(G(z)) → 0 on fake</text>
  <path class="d-line-accent" d="M708 186 V28 H240 V70" marker-end="url(#gan-arrow-accent)"/>
  <text class="d-title" x="474" y="46" text-anchor="middle">Train G: minimise the objective</text>
  <text class="d-note" x="474" y="61" text-anchor="middle">push D(G(z)) → 1; gradient flows back through D</text>
  <text class="d-math" x="430" y="440" text-anchor="middle">min<tspan dy="3">G</tspan><tspan dy="-3"> max</tspan><tspan dy="3">D</tspan><tspan dy="-3">   E</tspan><tspan dy="3">x</tspan><tspan dy="-3"> [ log D(x) ]  +  E</tspan><tspan dy="3">z</tspan><tspan dy="-3"> [ log(1 − D(G(z))) ]</tspan></text>
  <text class="d-note" x="430" y="459" text-anchor="middle">At the equilibrium the generator's distribution equals the data and D(·) = ½ everywhere</text>
  </svg>
diagram_caption: "The generator turns noise z into a fake sample; the discriminator scores real and fake samples with the probability \"real\". D is trained to tell them apart, and G is trained through D to fool it: one minimax objective."
notable_models:
  - { name: "GAN", year: 2014, note: "The original — proved a generator can learn from a discriminator's gradient alone, with no explicit likelihood." }
  - { name: "DCGAN", year: 2015, note: "Made GAN training stable with convolutional architectures and showed the latent space supports smooth interpolation and arithmetic." }
  - { name: "BigGAN", year: 2018, note: "Scaled GANs to high-resolution, class-conditional ImageNet synthesis, proving the recipe works at large scale." }
  - { name: "StyleGAN", year: 2019, note: "Generated photorealistic human faces and introduced disentangled style controls for pose, lighting, and identity." }

resources:
  - { kind: paper, title: "Generative Adversarial Nets (Goodfellow et al., 2014)", url: "https://arxiv.org/abs/1406.2661" }
  - { kind: blog, title: "From GAN to WGAN (Lilian Weng)", url: "https://lilianweng.github.io/posts/2017-08-20-gan/" }
  - { kind: tutorial, title: "DCGAN Tutorial (PyTorch)", url: "https://pytorch.org/tutorials/beginner/dcgan_faces_tutorial.html" }
  - { kind: video, title: "Generative Adversarial Networks explained (StatQuest)", url: "https://www.youtube.com/watch?v=8L11aMN5KY8" }

learn_first:
  - { label: "Neural networks & backpropagation", href: "/learn/concepts/backpropagation", tag: "Math" }
  - { label: "What a generative model is", href: "/learn/concepts/generative-models", tag: "Concept" }
  - { label: "Convolutional networks (the generator/discriminator backbone)", href: "/models/architecture/cnn", tag: "Architecture" }

tags: [gan, generation, adversarial, image-synthesis]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/1406.2661", title: "Generative Adversarial Nets (Goodfellow et al., 2014)", trust: 0.95 }
  - { url: "https://lilianweng.github.io/posts/2017-08-20-gan/", title: "From GAN to WGAN (Lilian Weng)", trust: 0.9 }
---

## What Changed
Generative models before GANs typically optimized an explicit likelihood — they tried to write down
the probability of every possible image — and that math pushed them toward blurry samples. Goodfellow
et al. reframed generation as a **game between two networks**. A generator maps random noise to
synthetic samples; a discriminator tries to tell real data from generated. They train in opposition —
the discriminator improves at spotting fakes, the generator improves at fooling it — and at the game's
equilibrium the generator's distribution matches the real data. Crucially this needs *no explicit
density*: the generator learns purely from the gradient the discriminator provides, a "likelihood-free"
approach that yields strikingly sharp images.

## Key Ideas
- **Adversarial minimax game** — generator and discriminator optimize opposing objectives; the
  generator learns from the discriminator's gradient rather than a reconstruction target.
- **Implicit generative model** — no tractable likelihood is computed; sampling is a single forward
  pass through the generator, which is fast at inference.
- **Sharpness** — the adversarial signal pushes toward realistic, high-frequency detail, giving
  crisper images than likelihood-based methods of the era.
- **Instability and mode collapse** — training can oscillate or diverge, and the generator may cover
  only part of the data distribution; much follow-up work (WGAN, spectral normalization) fought these
  issues, and they are exactly what made diffusion models attractive successors.
