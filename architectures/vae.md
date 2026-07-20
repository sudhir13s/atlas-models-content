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
description: "The likelihood-based latent-variable generative model that made deep variational inference practical."
key_features: ["Probabilistic encoder (approximate posterior)", "Continuous latent space", "Evidence lower bound (ELBO) objective", "Reparameterization trick", "KL-regularized latents"]
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
  motivation: "Plain autoencoders could compress an image to a small code and reconstruct it, but they were not generative. Their codes scattered into disconnected islands with empty gaps between them, so if you picked a random code and decoded it, you usually got garbage. Meanwhile, the principled tool for this — Bayesian latent-variable models with variational inference — did not scale to deep networks, because you could not backpropagate through the random sampling step at their core."
  approach: "Kingma and Welling married the two. Keep the encoder-decoder shape of an autoencoder, but make the encoder output a *distribution* over codes rather than a single point, and train the whole thing with variational inference. This forces the latent space to be smooth and gap-free, which makes it generative — and it stays trainable by ordinary backpropagation."
  solution: "The encoder maps each input to a small Gaussian in latent space (a mean and a spread). Training maximizes the evidence lower bound (ELBO): a reconstruction term that rewards rebuilding the input, minus a KL term that pulls every encoded Gaussian toward a shared standard-normal prior — the rule that keeps the space tidy. The trick that makes it all differentiable is the reparameterization trick: instead of sampling a code directly (a dead end for gradients), write the code as mean + spread × external noise, so the randomness sits outside the network and gradients flow straight through."
  outcome: "The result is a smooth, continuous latent space you can sample from and interpolate across — walk a straight line between two codes and the decoded images morph smoothly. VAEs trade some sample sharpness for stable training and excellent distribution coverage — the mirror image of GANs' failure profile, rarely collapsing. Their latent-variable, likelihood view directly underlies diffusion models (which can be read as deep hierarchical VAEs), and their compressed latent space is what latent diffusion runs inside."

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
  - "Stable, well-behaved training — a single likelihood-based objective with no adversarial game to balance."
  - "Smooth, continuous latent space you can sample and interpolate — meaningful codes, morphing reconstructions."
  - "Excellent distribution coverage — the model rarely collapses and represents the full data, not just a few modes."
  - "A principled probabilistic footing — gives a tractable likelihood bound and a reusable compressed representation."

weaknesses:
  - "Samples are often blurry — the reconstruction objective averages over plausible outputs instead of committing to sharp detail."
  - "The KL term can 'over-regularize' — if the decoder is too powerful the latent code gets ignored (posterior collapse)."
  - "Balancing reconstruction against the KL penalty is delicate and needs tuning (e.g. KL warm-up, β-VAE weighting)."
  - "The simple Gaussian prior/posterior can be too rigid to capture rich, multimodal data on its own."

diagram: |
  flowchart LR
    X["Input x"] --> ENC["Encoder"]
    ENC --> M["Mean μ"]
    ENC --> S["Spread σ"]
    M --> Z["Sample code z"]
    S --> Z
    N["Noise ε"] --> Z
    Z --> DEC["Decoder"]
    DEC --> XR["Reconstruction x̂"]
diagram_caption: "The encoder outputs a mean and spread; a code is sampled with external noise (reparameterization); the decoder rebuilds the input."

notable_models:
  - { name: "VAE", year: 2013, note: "The original — proved deep variational inference is trainable end-to-end via the reparameterization trick." }
  - { name: "β-VAE", year: 2017, note: "Reweighted the KL term to trade reconstruction for disentangled, interpretable latent factors." }
  - { name: "VQ-VAE", year: 2017, note: "Swapped the continuous latent for a discrete codebook, enabling sharp, high-fidelity generation and powering later image and audio models." }
  - { name: "NVAE", year: 2020, note: "A deep hierarchical VAE that closed much of the sharpness gap with GANs on face generation." }

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
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/1312.6114", title: "Auto-Encoding Variational Bayes (Kingma & Welling, 2013)", trust: 0.95 }
  - { url: "https://lilianweng.github.io/posts/2018-08-12-vae/", title: "From Autoencoder to Beta-VAE (Lilian Weng)", trust: 0.9 }
---

## What Changed
Kingma and Welling gave deep learning a principled, likelihood-based generative model. A VAE pairs an
**encoder** that maps a data point to a *distribution* over latent codes with a **decoder** that
reconstructs data from a sampled code. Training maximizes the **evidence lower bound (ELBO)**, which
balances two terms: reconstruction quality, and a KL penalty pulling the encoder's posterior toward a
simple prior (usually a standard Gaussian). The technical unlock is the **reparameterization trick** —
writing a sampled latent as mean + spread × external noise — which moves the randomness outside the
network so gradients can flow through the sampling step and the whole thing trains by ordinary
backpropagation.

## Key Ideas
- **Probabilistic encoder** — outputs a posterior *q(z|x)* rather than a point, so nearby inputs map to
  overlapping latent regions and the space stays continuous.
- **ELBO objective** — maximize a tractable lower bound on data likelihood: a reconstruction term minus
  a KL term regularizing the latent toward the prior.
- **Reparameterization trick** — factor sampling into a deterministic function of the parameters plus
  external noise, enabling gradient-based training through a stochastic node.
- **Coverage over sharpness** — VAE samples are often blurrier than GANs' but the model rarely collapses
  and covers the full data distribution; its latent-variable, likelihood view directly underlies
  diffusion models.
