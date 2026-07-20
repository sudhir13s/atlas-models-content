---
id: architecture-era/diffusion
type: architecture-era
slug: diffusion
schema_version: 1.0
title: "Diffusion Models"
name: "Diffusion Model"
era: post-transformer
era_range: "2020–2026"
year: 2020
family: generation
core_innovation: "Learn to reverse a gradual noising process, generating data by iteratively denoising from pure noise."
description: "Iterative denoising generative models that displaced GANs for high-fidelity, diverse image synthesis."
key_features: ["Forward Gaussian noising schedule", "Learned iterative denoiser", "Stable regression-based training", "Classifier-free guidance", "Latent-space diffusion for efficiency"]
key_models: ["DDPM (2020)", "Stable Diffusion / LDM (2022)", "Imagen (2022)"]
paper_title: "Denoising Diffusion Probabilistic Models (Ho, Jain & Abbeel, 2020)"
paper_url: "https://arxiv.org/abs/2006.11239"
influenced_by: [architecture-era/vae, architecture-era/gan]
led_to: []

eli5: "A diffusion model learns to un-blur noise into a picture. First you take real images and slowly add TV static until they are pure noise. Then you train a network to undo one tiny step of that. To create something new, start from random static and let the network clean it up, step by step, until a fresh image appears."

analogy:
  title: "Sculpting a statue out of static"
  body: "A sculptor starts with a rough block of marble and chips away, a little at a time, until a figure emerges. Diffusion works the same way, but the block is a screen of random TV static and the chisel is a denoiser. Here is the clever part: the model learned to sculpt by watching the reverse — take a clean photo and slowly bury it under static, one thin layer at a time, keeping a record of exactly what static was added at each step. Reverse that recording and you have a step-by-step guide for removing static. To make a new image, hand the model fresh random static and let it 'chip away' the noise step by step. Because each step only has to remove a thin layer, every step is easy — which is why the whole thing trains so reliably."

narrative:
  motivation: "By 2020 the two leading generative families each had a fatal flaw. Generative adversarial networks (GANs) made sharp images but their adversarial game was unstable and prone to mode collapse — covering only a slice of the data. Variational autoencoders (VAEs) trained stably and covered the full distribution but produced blurry samples. Everyone wanted a model with both properties: the fidelity of a GAN and the stability and coverage of a VAE. The obstacle was that generating a realistic image in one shot is a very hard prediction — too hard to do well and reliably at the same time."
  approach: "The insight was to stop trying to generate an image in one leap and instead break generation into hundreds of tiny, easy steps. Define a fixed forward process that slowly adds a little Gaussian noise to a real image over many steps until nothing but static remains. Then train a single network to reverse just one step of that process — a much gentler task than conjuring an image from nothing."
  solution: "The forward noising schedule is fixed and parameter-free, so at every step the model knows exactly what noise was added — that noise is the training target. The network learns a simple regression: given a noisy image and the step number, predict the noise that was mixed in (equivalently, a slightly cleaner image). Because each step is an easy least-squares target, training is remarkably stable and the model covers the full distribution. To generate, start from pure noise and apply the learned denoiser repeatedly, walking backward step by step to a clean sample."
  outcome: "Diffusion got both stability and fidelity, and displaced GANs as the dominant image generator. DDPM (Ho et al., 2020) established the recipe. Latent Diffusion / Stable Diffusion (Rombach et al., 2022) made it practical by running the diffusion inside a compressed VAE latent space instead of on raw pixels, cutting compute enough for high-resolution text-to-image on commodity GPUs. Combined with classifier-free guidance to steer samples toward a text prompt, diffusion became the backbone of modern image, audio, and video generation."

build_steps:
  - { title: "1. Define the forward noising schedule", detail: "Fix a plan for how much Gaussian noise to add at each of many timesteps. This process is parameter-free — nothing is learned here; it just gradually turns any real image into pure static." }
  - { title: "2. Make a noisy training example", detail: "Take a real image, pick a random timestep, and jump straight to that noise level in one shot (the math lets you skip the intermediate steps). Remember the exact noise you added — that is the answer key." }
  - { title: "3. Predict the noise", detail: "Feed the noisy image and the timestep into the network (usually a U-Net). It predicts the noise that was added. This is a plain regression — no adversary, no likelihood to compute." }
  - { title: "4. Train on the error", detail: "Compare predicted noise to the real noise and minimize the difference. Repeat across many images, timesteps, and noise draws. Every step is an easy target, which is why training is stable." }
  - { title: "5. Sample by denoising from static", detail: "To generate, start from pure random noise. Use the trained network to estimate and remove a bit of noise, step by step, walking backward through the schedule until a clean image emerges." }
  - { title: "6. Steer with guidance (optional)", detail: "To follow a text prompt, condition the denoiser on the prompt and use classifier-free guidance — nudge each step toward the prompt-aware prediction and away from the unconditioned one." }

key_equation:
  latex: "L = \\mathbb{E}_{t,\\,x_0,\\,\\epsilon}\\left[\\; \\bigl\\lVert \\epsilon - \\epsilon_\\theta\\!\\left(\\sqrt{\\bar{\\alpha}_t}\\,x_0 + \\sqrt{1-\\bar{\\alpha}_t}\\,\\epsilon,\\; t\\right) \\bigr\\rVert^2 \\;\\right]"
  plain: "Take a clean image, mix in a known amount of random noise to land at timestep t, and ask the network to guess the noise it sees. The loss is simply the squared difference between the real noise and the network's guess. Minimizing it teaches the network to denoise — and that is all you need to generate images by removing noise step by step."
  terms:
    - { symbol: "x₀", meaning: "a clean training image (before any noise is added)" }
    - { symbol: "ε", meaning: "the random Gaussian noise actually mixed in — the target the network must predict" }
    - { symbol: "ε_θ(·)", meaning: "the network's predicted noise, given the noisy image and timestep" }
    - { symbol: "t", meaning: "the timestep — how far along the noising schedule this example is" }
    - { symbol: "ᾱ_t", meaning: "the schedule's cumulative signal level at step t; √ᾱ_t scales the image and √(1−ᾱ_t) scales the noise" }

strengths:
  - "Stable, reliable training — a plain noise-prediction regression, no adversarial game to balance."
  - "Excellent coverage and diversity — models the full data distribution and rarely collapses to a few modes."
  - "State-of-the-art fidelity — matches or beats GANs on image quality, especially with guidance."
  - "Flexible conditioning — text, layout, depth, or another image can steer generation cleanly."

weaknesses:
  - "Slow sampling — generating one image needs many sequential denoising steps (though fast samplers and distillation help)."
  - "Compute-heavy on raw pixels — the reason latent diffusion moves the process into a compressed space."
  - "Guidance is a balancing act — too much sharpens prompt-following but hurts diversity and can over-saturate."
  - "Reproducing fine text or exact structure in images remains hard without extra conditioning."

diagram: |
  flowchart LR
    X0["Clean image x₀"] -->|"add noise, step by step"| XT["Pure noise"]
    XT -->|"denoise with network"| D1["Less noisy"]
    D1 -->|"predict and remove noise"| D2["Cleaner"]
    D2 -->|"repeat T steps"| OUT["Generated image"]
    P["Text prompt"] -.->|"guidance"| D1
    P -.->|"guidance"| D2
diagram_caption: "Forward: real image to noise (fixed). Reverse: the network denoises from static back to an image, optionally steered by a prompt."

notable_models:
  - { name: "DDPM", year: 2020, note: "The breakthrough recipe — showed simple noise-prediction denoising rivals GANs on image quality with stable training." }
  - { name: "Latent Diffusion / Stable Diffusion", year: 2022, note: "Ran diffusion in a compressed VAE latent space, making high-resolution text-to-image cheap enough for commodity GPUs and open release." }
  - { name: "Imagen", year: 2022, note: "Showed a large frozen text encoder plus diffusion yields strong prompt-following photorealism." }
  - { name: "DALL·E 2", year: 2022, note: "Popularized diffusion text-to-image at scale and demonstrated CLIP-guided image generation." }

resources:
  - { kind: paper, title: "Denoising Diffusion Probabilistic Models (Ho, Jain & Abbeel, 2020)", url: "https://arxiv.org/abs/2006.11239" }
  - { kind: blog, title: "What are Diffusion Models? (Lilian Weng)", url: "https://lilianweng.github.io/posts/2021-07-11-diffusion-models/" }
  - { kind: tutorial, title: "The Annotated Diffusion Model (Hugging Face)", url: "https://huggingface.co/blog/annotated-diffusion" }
  - { kind: paper, title: "High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2022)", url: "https://arxiv.org/abs/2112.10752" }

learn_first:
  - { label: "Variational autoencoders (latent-variable view)", href: "/models/architecture/vae", tag: "Architecture" }
  - { label: "The Gaussian & adding noise", href: "/learn/concepts/gaussian", tag: "Math" }
  - { label: "U-Net (the denoiser backbone)", href: "/models/architecture/unet", tag: "Architecture" }

tags: [diffusion, generation, denoising, text-to-image]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2006.11239", title: "Denoising Diffusion Probabilistic Models (Ho, Jain & Abbeel, 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2112.10752", title: "High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2022)", trust: 0.95 }
  - { url: "https://lilianweng.github.io/posts/2021-07-11-diffusion-models/", title: "What are Diffusion Models? (Lilian Weng)", trust: 0.9 }
---

## What Changed
GANs made sharp images but were unstable and prone to mode collapse; VAEs were stable but blurry.
Diffusion models got both stability *and* fidelity by splitting generation into many easy steps. The
**forward process** takes a real image and adds Gaussian noise over many steps until it is
indistinguishable from static — a fixed, parameter-free schedule. A network is then trained to run the
**reverse process**: at each step, predict the noise (equivalently, a slightly denoised image), so that
starting from pure noise and denoising repeatedly reconstructs a sample from the data distribution.
Each step is a simple regression target, which makes training remarkably stable and the model cover the
full distribution.

## Key Ideas
- **Forward noising** — a fixed schedule gradually corrupts data into Gaussian noise, defining the
  targets the model learns to reverse.
- **Learned denoising** — the network predicts the noise at each timestep; sampling chains many such
  predictions from noise back to a clean sample.
- **Stable training, full coverage** — per-step regression avoids adversarial instability and mode
  collapse, trading single-shot speed for many inference steps.
- **Latent diffusion + guidance** — diffusing in a VAE latent space slashes cost, and classifier-free
  guidance steers samples toward a text prompt, enabling controllable text-to-image generation.
