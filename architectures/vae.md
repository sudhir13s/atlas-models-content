---
id: architecture-era/vae
type: architecture-era
slug: vae
schema_version: 1.0
title: "Variational Autoencoder"
name: "Variational Autoencoder"
era: foundation
era_range: "2013–2020"
year: 2013
family: generation
core_innovation: "A probabilistic encoder-decoder trained by variational inference with the reparameterization trick, learning a continuous latent space."
description: "A latent-variable generative model trained by stochastic variational inference that scales to large datasets, with a neural-network encoder approximating the posterior."
key_features: ["Probabilistic encoder (approximate posterior)", "Evidence lower bound (ELBO) objective", "Reparameterization trick"]
key_models: ["VAE (2013)", "VQ-VAE (2017)", "NVAE (2020)"]
paper_title: "Auto-Encoding Variational Bayes (Kingma & Welling, 2013)"
paper_url: "https://arxiv.org/abs/1312.6114"
influenced_by: []
led_to: [architecture-era/diffusion]

eli5: "A VAE learns to squeeze an image down to a few numbers and then rebuild it. It is trained so that the squeezed numbers live in a tidy, gap-free space — so you can pick a random point in that space, decode it, and get a brand-new image that looks like your training data."

analogy:
  title: "A tidy compression cabinet you can also invent from"
  body: "Imagine an artist who learns to describe any face with a short recipe: 'wide jaw, warm skin, slight smile'. That is the encoder compressing an image into a few numbers. A second artist reads a recipe and paints the face back — that is the decoder. A plain autoencoder would let the recipes scatter into isolated islands with empty gaps between them, so a made-up recipe decodes to nonsense. The VAE adds one rule: recipes must fill a smooth, connected space with no gaps, packed neatly around a simple center. Now you can invent a recipe you have never seen — pick any point in that tidy space — and the decoder paints a plausible new face. The 'keep the space tidy' rule is the KL penalty; encoding a *fuzzy region* instead of a single point is what fills the gaps."

narrative:
  motivation: "Kingma and Welling asked how to perform efficient inference and learning in directed probabilistic models with continuous latent variables, intractable posterior distributions and large datasets. Variational inference offered a lower bound on the likelihood, but it needed an estimator that standard stochastic gradient methods could optimize."
  approach: "Keep the encoder-decoder shape of an autoencoder, but make the encoder (a 'recognition model') output a *distribution* over codes rather than a single point, and train encoder and decoder jointly on a variational lower bound. A reparameterization of the bound makes it optimizable with ordinary stochastic gradients."
  solution: "The encoder maps each input to a small Gaussian in latent space (a mean and a spread). Training maximizes the evidence lower bound (ELBO): a reconstruction term that rewards rebuilding the input, minus a KL term that pulls every encoded Gaussian toward a shared standard-normal prior — the rule that keeps the space tidy. The trick that makes it all differentiable is the reparameterization trick: instead of sampling a code directly (a dead end for gradients), write the code as mean + spread × external noise, so the randomness sits outside the network and gradients flow straight through."
  outcome: "The result is a continuous latent space you can sample from: the paper's appendix shows learned 2D manifolds of MNIST digits and Frey faces, decoded from a grid of latent coordinates. Diffusion models are also latent-variable models trained on a variational bound (Ho et al., 2020), and latent diffusion runs diffusion in the latent space of a pretrained autoencoder (Rombach et al., 2021)."

build_steps:
  - { title: "1. Encode to a distribution", detail: "Pass the input through the encoder, which outputs two vectors: a mean and a spread (standard deviation). Together they define a small Gaussian cloud in latent space — the model's belief about where this input's code lives." }
  - { title: "2. Sample a code — the reparameterization trick", detail: "Draw external noise from a standard normal, then set code = mean + spread × noise. This produces a random code but keeps the randomness outside the network, so gradients can flow back through the mean and spread." }
  - { title: "3. Decode back to data", detail: "Feed the sampled code through the decoder to reconstruct the input. The decoder is the generator you will use later to turn codes into new samples." }
  - { title: "4. Score reconstruction", detail: "Measure how close the reconstruction is to the original input. This is the first half of the loss — it rewards codes that carry enough information to rebuild the data." }
  - { title: "5. Regularize the latent space (KL term)", detail: "Add a penalty pulling each encoded Gaussian toward the standard-normal prior. This is what fills the gaps and keeps the space tidy, so random codes decode to something sensible." }
  - { title: "6. Generate new data", detail: "After training, throw away the encoder: sample a code straight from the standard normal, run it through the decoder, and get a brand-new sample that resembles the training data." }

key_equation:
  latex: "\\log p(x) \\;\\ge\\; \\underbrace{\\mathbb{E}_{q(z\\mid x)}\\!\\left[\\log p(x\\mid z)\\right]}_{\\text{reconstruction}} \\;-\\; \\underbrace{D_{\\mathrm{KL}}\\!\\left(q(z\\mid x)\\,\\|\\,p(z)\\right)}_{\\text{stay tidy}}"
  plain: "We cannot compute the true likelihood of the data directly, so we maximize a lower bound on it — the evidence lower bound (ELBO). It has two parts pulling against each other: the reconstruction term rewards codes that let the decoder rebuild the input, while the KL term pushes every encoded distribution toward a simple standard-normal prior so the latent space stays smooth and gap-free. Push the bound up and both goals improve together."
  terms:
    - { symbol: "log p(x)", meaning: "the (intractable) log-likelihood of the data we ultimately want to maximize" }
    - { symbol: "q(z|x)", meaning: "the encoder — the approximate posterior, a Gaussian over codes z given input x" }
    - { symbol: "p(x|z)", meaning: "the decoder — the probability of reconstructing x from a code z" }
    - { symbol: "p(z)", meaning: "the prior over codes, a standard normal N(0, I) — the 'tidy center' the KL term pulls toward" }
    - { symbol: "D_KL", meaning: "Kullback–Leibler (KL) divergence — how far the encoder's distribution is from the prior" }

strengths:
  - "A single objective, the variational lower bound, optimized with standard stochastic gradient methods."
  - "A continuous latent space you can sample from and decode into new data."
  - "Fast sampling and an encoder network that is easy to access, as the NVAE authors note."
  - "A probabilistic footing: a tractable lower bound on the data likelihood and a reusable compressed representation."

weaknesses:
  - "Posterior collapse: paired with a powerful autoregressive decoder, the latents can be ignored (noted in the VQ-VAE paper)."
  - "As of 2020, VAEs were outperformed on likelihood by normalizing flows and autoregressive models, per the NVAE authors."

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 470" role="img" aria-label="A variational autoencoder: the encoder maps x to a mean μ and a spread σ, a separate noise sample ε gives the latent z = μ + σ ⊙ ε, and the decoder rebuilds x̂ from the narrow latent; the loss is reconstruction of x̂ against x plus a KL term pulling q(z|x) toward N(0, I).">
  <defs><marker id="vae-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="vae-arrow-thin" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="vae-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <rect class="d-box d-role-input" x="46" y="140" width="20" height="20"/><rect class="d-box d-role-input" x="46" y="160" width="20" height="20"/><rect class="d-box d-role-input" x="46" y="180" width="20" height="20"/><rect class="d-box d-role-input" x="46" y="200" width="20" height="20"/><rect class="d-box d-role-input" x="46" y="220" width="20" height="20"/><rect class="d-box d-role-input" x="46" y="240" width="20" height="20"/><rect class="d-box d-role-input" x="46" y="260" width="20" height="20"/>
  <text class="d-math" x="56" y="302" text-anchor="middle">x</text>
  <text class="d-note" x="56" y="316" text-anchor="middle">input</text>
  <rect class="d-box d-role-output" x="794" y="140" width="20" height="20"/><rect class="d-box d-role-output" x="794" y="160" width="20" height="20"/><rect class="d-box d-role-output" x="794" y="180" width="20" height="20"/><rect class="d-box d-role-output" x="794" y="200" width="20" height="20"/><rect class="d-box d-role-output" x="794" y="220" width="20" height="20"/><rect class="d-box d-role-output" x="794" y="240" width="20" height="20"/><rect class="d-box d-role-output" x="794" y="260" width="20" height="20"/>
  <text class="d-math" x="804" y="302" text-anchor="middle">x̂</text>
  <text class="d-note" x="804" y="316" text-anchor="middle">reconstruction</text>
  <path class="d-line-strong" d="M70 210 H102" marker-end="url(#vae-arrow)"/>
  <polygon class="d-box d-role-feedforward" points="106,136 256,174 256,246 106,284"/>
  <text class="d-title" x="176" y="207" text-anchor="middle">Encoder</text>
  <text class="d-math" x="176" y="223" text-anchor="middle">q<tspan dy="3">φ</tspan><tspan dy="-3">(z | x)</tspan></text>
  <path class="d-line-strong" d="M256 190 L284 182" marker-end="url(#vae-arrow)"/>
  <path class="d-line-strong" d="M256 230 L284 242" marker-end="url(#vae-arrow)"/>
  <rect class="d-box d-role-attention" x="288" y="168" width="48" height="28" rx="5"/>
  <text class="d-math" x="312" y="186" text-anchor="middle">μ</text>
  <rect class="d-box d-role-attention" x="288" y="232" width="48" height="28" rx="5"/>
  <text class="d-math" x="312" y="250" text-anchor="middle">σ</text>
  <rect class="d-frame" x="370" y="128" width="136" height="196" rx="6"/>
  <text class="d-note" x="438" y="144" text-anchor="middle">reparameterisation</text>
  <circle class="d-node" cx="420" cy="246" r="12"/>
  <text class="d-label" x="420" y="250" text-anchor="middle">⊙</text>
  <circle class="d-node" cx="470" cy="210" r="12"/>
  <text class="d-label" x="470" y="214" text-anchor="middle">+</text>
  <path class="d-line-accent" d="M336 246 H405" marker-end="url(#vae-arrow-accent)"/>
  <path class="d-line-accent" d="M336 182 H470 V195" marker-end="url(#vae-arrow-accent)"/>
  <path class="d-line-accent" d="M432 246 L462 219" marker-end="url(#vae-arrow-accent)"/>
  <rect class="d-box d-role-router" x="380" y="282" width="80" height="28" rx="5"/>
  <text class="d-math" x="420" y="300" text-anchor="middle">ε ~ N(0, I)</text>
  <path class="d-line" d="M420 282 V261" marker-end="url(#vae-arrow-thin)"/>
  <text class="d-math" x="438" y="342" text-anchor="middle">z = μ + σ ⊙ ε</text>
  <text class="d-note" x="438" y="357" text-anchor="middle">noise sits outside the network,</text>
  <text class="d-note" x="438" y="370" text-anchor="middle">so ∂L/∂μ and ∂L/∂σ exist</text>
  <path class="d-line-accent" d="M482 210 H524" marker-end="url(#vae-arrow-accent)"/>
  <rect class="d-box d-role-attention" x="528" y="180" width="30" height="60" rx="5"/>
  <text class="d-math" x="543" y="214" text-anchor="middle">z</text>
  <text class="d-note" x="543" y="256" text-anchor="middle">latent</text>
  <text class="d-note" x="543" y="269" text-anchor="middle">bottleneck</text>
  <path class="d-line-strong" d="M558 210 H586" marker-end="url(#vae-arrow)"/>
  <polygon class="d-box d-role-feedforward" points="590,174 740,136 740,284 590,246"/>
  <text class="d-title" x="670" y="207" text-anchor="middle">Decoder</text>
  <text class="d-math" x="670" y="223" text-anchor="middle">p<tspan dy="3">θ</tspan><tspan dy="-3">(x | z)</tspan></text>
  <path class="d-line-strong" d="M740 210 H790" marker-end="url(#vae-arrow)"/>
  <rect class="d-box d-role-router" x="236" y="30" width="196" height="46" rx="5"/>
  <text class="d-title" x="334" y="50" text-anchor="middle">KL( q<tspan dy="3">φ</tspan><tspan dy="-3">(z | x) ‖ N(0, I) )</tspan></text>
  <text class="d-note" x="334" y="66" text-anchor="middle">pulls every q(z|x) toward the prior</text>
  <rect class="d-frame" x="280" y="160" width="64" height="108" rx="5"/>
  <path class="d-line" d="M312 160 V80" marker-end="url(#vae-arrow-thin)"/>
  <path class="d-line-back" d="M56 322 V408 H804 V322"/>
  <rect class="d-box d-role-router" x="300" y="392" width="260" height="32" rx="5"/>
  <text class="d-title" x="430" y="412" text-anchor="middle">Reconstruction  −log p<tspan dy="3">θ</tspan><tspan dy="-3">(x | z)</tspan></text>
  <text class="d-note" x="430" y="444" text-anchor="middle">Loss = reconstruction + KL, the negative evidence lower bound (ELBO), minimised over φ and θ</text>
  </svg>
diagram_caption: "The encoder outputs a mean μ and spread σ; outside noise ε gives the latent z = μ + σ ⊙ ε, so gradients reach μ and σ. The decoder rebuilds x̂; the loss adds reconstruction and a KL pull toward N(0, I)."
notable_models:
  - { name: "VAE", year: 2013, note: "The original: encoder and decoder trained jointly on a reparameterized variational lower bound with stochastic gradients." }
  - { name: "VQ-VAE", year: 2017, note: "Discrete rather than continuous codes, via vector quantization, and a learnt prior; generated images, video and speech." }
  - { name: "NVAE", year: 2020, note: "A deep hierarchical VAE with state-of-the-art results among non-autoregressive likelihood models; the authors report it as the first successful VAE on natural images as large as 256×256." }

resources:
  - { kind: paper, title: "Auto-Encoding Variational Bayes (Kingma & Welling, 2013)", url: "https://arxiv.org/abs/1312.6114" }
  - { kind: blog, title: "From Autoencoder to Beta-VAE (Lilian Weng)", url: "https://lilianweng.github.io/posts/2018-08-12-vae/" }
  - { kind: tutorial, title: "Variational Autoencoder tutorial (Keras)", url: "https://keras.io/examples/generative/vae/" }
  - { kind: video, title: "Variational Autoencoders (Arxiv Insights)", url: "https://www.youtube.com/watch?v=9zKuYvjFFS8" }

learn_first:
  - { label: "Autoencoders (compress then reconstruct)", href: "/models/architecture/autoencoder", tag: "Architecture" }
  - { label: "Probability distributions & the Gaussian", href: "/learn/concepts/gaussian", tag: "Math" }
  - { label: "KL divergence", href: "/learn/concepts/kl-divergence", tag: "Math" }

tags: [vae, generation, latent-variable, variational-inference]
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/1312.6114", title: "Auto-Encoding Variational Bayes (Kingma & Welling, 2013)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1711.00937", title: "Neural Discrete Representation Learning (van den Oord et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2007.03898", title: "NVAE: A Deep Hierarchical Variational Autoencoder (Vahdat and Kautz, 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2006.11239", title: "Denoising Diffusion Probabilistic Models (Ho et al., 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2112.10752", title: "High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2021)", trust: 0.95 }
  - { url: "https://lilianweng.github.io/posts/2018-08-12-vae/", title: "From Autoencoder to Beta-VAE (Lilian Weng)", trust: 0.9 }
---

## What Changed
Kingma and Welling (2013) pair an **encoder** that maps a data point to a *distribution* over latent
codes with a **decoder** that reconstructs data from a sampled code. Training maximizes the **evidence
lower bound (ELBO)**: reconstruction quality minus a Kullback–Leibler (KL) term pulling the encoder's
posterior toward the prior (a centered isotropic Gaussian in the paper's example).

The technical step is the **reparameterization trick**: writing a sampled latent as mean + spread ×
external noise, so gradients flow through the sampling step and the model trains by backpropagation.

## Key Ideas
- **Probabilistic encoder**: outputs a posterior *q(z|x)*, for example a Gaussian, rather than a point.
- **ELBO objective**: maximize a tractable lower bound on data likelihood, a reconstruction term minus
  a KL term toward the prior.
- **Reparameterization trick**: express sampling as a deterministic function of the parameters plus
  external noise, enabling gradient-based training through a stochastic node.
- **Related models**: diffusion models are also latent-variable models trained on a variational bound
  (Ho et al., 2020).
