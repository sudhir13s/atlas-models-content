---
id: architecture-era/diffusion
type: architecture-era
slug: diffusion
schema_version: 2.5
title: "Diffusion Models"
name: "Diffusion Model"
era: post-transformer
era_range: "2020–2026"
year: 2020
family: generation
core_innovation: "Learn to reverse a gradual noising process, generating data by iteratively denoising from pure noise."
description: "A generative model that learns to undo noise: training images are buried under random noise step by step, a network learns to remove it a little at a time, and a new image is made by starting from pure noise and denoising it step by step."
key_features: ["Fixed forward process that gradually adds Gaussian noise", "Learned reverse (denoising) process", "A network trained to predict the added noise with a squared error", "Sampling by many sequential denoising steps"]
key_models: ["DDPM (2020)", "Stable Diffusion / LDM (2022)", "Imagen (2022)"]
paper_title: "Deep Unsupervised Learning using Nonequilibrium Thermodynamics (Sohl-Dickstein et al., 2015)"
paper_url: "https://arxiv.org/abs/1503.03585"
influenced_by: [architecture-era/vae, architecture-era/gan]
led_to: []

analogy:
  title: "Restoring a photo buried under static"
  body: "This is an analogy, not the mechanism. Imagine burying a photo under one thin layer of TV static after another until nothing of it shows. A restorer practises on thousands of photos, each time told exactly which static was added, and learns to guess the static from what is left. To make a new picture, the restorer is handed pure static and peels away a little of their best guess, over and over, until a clean image remains. The restorer is the network, a layer of static is one noising step, and the guess is the predicted noise."

narrative:
  motivation: "**Flexible models were hard to train and sample; tractable ones were too simple.** The idea comes from **Sohl-Dickstein et al. (2015)**.\n\n- **The trade-off:** the authors write that probabilistic models had to choose between being **tractable** (easy to fit and evaluate, like a Gaussian) and **flexible** (able to fit rich data).\n- **The aim:** a model that is both, with learning, sampling and evaluation all workable."
  approach: "**Destroy structure slowly, then learn to restore it.**\n\n- **Forward diffusion:** a fixed process that **systematically and slowly destroys structure** in the data, step by step.\n- **Reverse diffusion:** a learned process that **restores** it, one small step at a time.\n- **Why small steps:** the authors note that estimating small changes to a diffusion process is **more tractable** than describing the whole distribution at once; their image experiments use **T = 1000** steps."
  solution: "**DDPM: predict the noise, with a squared error.** By **Ho, Jain and Abbeel (2020)**.\n\n- **Fixed noise schedule:** Gaussian noise with variances rising linearly from **β₁ = 0.0001** to **β_T = 0.02** over **T = 1000** steps.\n- **One jump to any step:** a noisy image at step t can be made from the clean one in a single formula, so training picks a **random t** each time.\n- **The network:** a **U-Net** that takes the noisy image and t and predicts the **noise** that was added.\n- **The loss:** the squared difference between the real and the predicted noise, a simplified objective the authors found gave the **best sample quality**."
  outcome: "**Benchmark results, then text-to-image models built on the idea.**\n\n- **CIFAR-10:** DDPM reports a Fréchet Inception Distance (FID) of **3.17** and an Inception score of **9.46**; on 256 × 256 LSUN, sample quality **similar to ProgressiveGAN**.\n- **The cost of sampling:** **1000** network evaluations per image.\n- **Latent diffusion (Rombach et al., 2021):** runs the process in the **latent space** of a pre-trained autoencoder to cut compute, and adds **cross-attention** for conditioning on text or bounding boxes; Stable Diffusion is built this way.\n- **Classifier-free guidance (Ho and Salimans, 2022):** steers a conditional model toward its condition without a separate classifier."

key_equation:
  latex: "L_{\\text{simple}} = \\mathbb{E}_{t,\\,x_0,\\,\\epsilon}\\left[\\; \\bigl\\lVert \\epsilon - \\epsilon_\\theta\\!\\left(\\sqrt{\\bar{\\alpha}_t}\\,x_0 + \\sqrt{1-\\bar{\\alpha}_t}\\,\\epsilon,\\; t\\right) \\bigr\\rVert^2 \\;\\right]"
  plain: "In words: take a clean image x₀, pick a random step t and a random noise ε, mix them into the noisy image √ᾱ_t·x₀ + √(1−ᾱ_t)·ε, ask the network to guess ε from that noisy image and t, and score the guess by the squared difference. Training averages this over many images, steps and noise draws. The worked example above does one such step by hand (Eq. 14 of the DDPM paper)."
  terms:
    - { symbol: "x₀", meaning: "a clean training image, its pixel values scaled from 0 to 255 down to −1 to 1" }
    - { symbol: "ε", meaning: "the noise mixed in: one random number per pixel from a standard Gaussian (mean 0, variance 1); the network's target" }
    - { symbol: "ε_θ(·, t)", meaning: "the network's guess of the noise, given the noisy image and the step t; θ are its learned weights" }
    - { symbol: "t", meaning: "the step, drawn uniformly from 1 to T (T = 1000 in DDPM); larger t means more noise" }
    - { symbol: "ᾱ_t", meaning: "the share of the image's variance still left at step t: the product of (1 − β_s) for s = 1 to t; √ᾱ_t scales the image and √(1−ᾱ_t) the noise" }
    - { symbol: "β_t", meaning: "the noise variance added at step t; DDPM raises it linearly from 0.0001 to 0.02" }

strengths:
  - "**A simple training objective:** a **squared error** between the real and the predicted noise, with no second network to compete against (DDPM, Eq. 14)."
  - "**Sample quality:** DDPM reports **FID 3.17** on unconditional CIFAR-10, and LSUN 256 × 256 samples of quality **similar to ProgressiveGAN**."
  - "**Stable training and coverage:** the latent diffusion authors write that, being likelihood-based, diffusion models **do not exhibit the mode collapse and training instabilities** of GANs."
  - "**Flexible conditioning:** latent diffusion adds **cross-attention** layers so the denoiser can follow text, bounding boxes or other inputs."
  - "**Guidance without a classifier:** classifier-free guidance uses **one network** for the conditional and unconditional model, so it does **not increase the number of parameters**, its authors write."

weaknesses:
  - "**Slow sampling:** DDPM runs the network **1000 times** for each sample; on a TPU v3-8, a batch of 256 CIFAR-10 images takes **17 seconds** and 128 LSUN images **300 seconds**."
  - "**Expensive in pixel space:** the latent diffusion authors note that training the strongest pixel-space models takes **hundreds of GPU days** (**150 to 1000 V100 days** in the work they cite)."
  - "**Weaker likelihoods:** DDPM's authors write that their models do **not have competitive log likelihoods** compared with other likelihood-based models."
  - "**Guidance has a price:** classifier-free guidance raises sample quality **at the expense of diversity**, and needs **two network passes** per step, one with and one without the condition."

comparison:
  before: "Generative adversarial network (GAN)"
  after: "Diffusion model"
  rows:
    - { aspect: "How training behaves", before: "A generator trained against a discriminator; the latent diffusion authors call GANs difficult to optimize", after: "Likelihood-based; the same authors write diffusion models do not exhibit the mode collapse and training instabilities of GANs", source: "https://arxiv.org/abs/2112.10752" }
    - { aspect: "Covering the whole range of the data", before: "GANs struggle to capture the full data distribution", after: "Mode-covering, which also spends capacity on details the eye cannot see", source: "https://arxiv.org/abs/2112.10752" }
    - { aspect: "Making one sample", before: "Efficient sampling of high-resolution images", after: "The same network runs sequentially for many steps (25 to 1000 in the work the authors cite, where 50,000 samples took about 5 days on one A100 GPU)", source: "https://arxiv.org/abs/2112.10752" }

key_numbers:
  columns: ["Setting", "Value", "Model"]
  rows:
    - { cells: ["Diffusion steps (T)", "1000", "DDPM, all experiments"], source: "https://arxiv.org/abs/2006.11239" }
    - { cells: ["Noise variance per step (β_t)", "rising linearly from 0.0001 to 0.02", "DDPM"], source: "https://arxiv.org/abs/2006.11239" }
    - { cells: ["U-Net parameters", "35.7 million", "DDPM, CIFAR-10 (32 × 32)"], source: "https://arxiv.org/abs/2006.11239" }
    - { cells: ["U-Net parameters", "114 million", "DDPM, LSUN and CelebA-HQ (256 × 256)"], source: "https://arxiv.org/abs/2006.11239" }
    - { cells: ["Training", "800,000 steps at batch size 128, 10.6 hours on a TPU v3-8", "DDPM, CIFAR-10"], source: "https://arxiv.org/abs/2006.11239" }
    - { cells: ["FID, unconditional CIFAR-10 (lower is better)", "3.17 (Inception score 9.46)", "DDPM"], source: "https://arxiv.org/abs/2006.11239" }
    - { cells: ["FID, LSUN 256 × 256", "Church 7.89, Bedroom 4.90", "DDPM"], source: "https://arxiv.org/abs/2006.11239" }
    - { cells: ["Latent the denoiser works on, for a 512 × 512 image", "64 × 64 × 4 (autoencoder downsampling factor 8)", "Stable Diffusion v1-4"], source: "https://huggingface.co/CompVis/stable-diffusion-v1-4" }
    - { cells: ["Text encoder", "CLIP ViT-L/14, frozen, fed in through cross-attention", "Stable Diffusion v1-4"], source: "https://huggingface.co/CompVis/stable-diffusion-v1-4" }

misunderstandings:
  - misunderstanding: "The model learns how to add the noise as well as how to remove it."
    truth: "The forward, noise-adding process is **fixed**. DDPM sets its variances β_t to constants, so that process has **no learnable parameters**; only the reverse, denoising network is trained (Section 3.1)."
    source: "https://arxiv.org/abs/2006.11239"
  - misunderstanding: "Training runs an image through all 1000 noising steps every time."
    truth: "Because each step adds Gaussian noise, the noisy image at **any** step t can be made in **one jump**: √ᾱ_t·x₀ + √(1−ᾱ_t)·ε (Eq. 4). Each training step draws **one random t** for each image (Algorithm 1). Only **sampling** walks through all 1000 steps."
    source: "https://arxiv.org/abs/2006.11239"
  - misunderstanding: "The network looks at a noisy image and outputs the clean image."
    truth: "In DDPM the network predicts the **noise**. The authors report that predicting the clean image directly led to **worse sample quality** early in their experiments. Predicting the noise with the simplified loss gave **FID 3.17**, against **13.22** for the best variant that predicts the step's mean (Table 2)."
    source: "https://arxiv.org/abs/2006.11239"
  - misunderstanding: "Stable Diffusion denoises the pixels of the image."
    truth: "Stable Diffusion is a **latent** diffusion model. An autoencoder first maps an H × W × 3 image to a latent of **H/8 × W/8 × 4**, for example 64 × 64 × 4 for a 512 × 512 image, and the U-Net is trained to predict the noise added to that latent. The latent holds 16,384 numbers against 786,432 in the image, 48 times fewer."
    source: "https://huggingface.co/CompVis/stable-diffusion-v1-4"

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 520" role="img" aria-label="A denoising diffusion model: the fixed forward process adds Gaussian noise step by step, turning a ring of dots x₀ into pure noise x_T, and the learned reverse process removes it step by step; below, one training step: a U-Net takes the noisy xₜ and t and predicts the added noise, trained with the squared error to the true noise.">
  <defs><marker id="diffusion-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="diffusion-arrow-thin" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="diffusion-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <text class="d-label" x="430" y="26" text-anchor="middle">Forward process q(x<tspan dy="3">t</tspan><tspan dy="-3"> | x</tspan><tspan dy="3">t−1</tspan><tspan dy="-3">) = N(√(1−β</tspan><tspan dy="3">t</tspan><tspan dy="-3">) x</tspan><tspan dy="3">t−1</tspan><tspan dy="-3">, β</tspan><tspan dy="3">t</tspan><tspan dy="-3">I)</tspan>: fixed, no learned parameters</text>
  <path class="d-line-strong" d="M40 40 H816" marker-end="url(#diffusion-arrow)"/>
  <rect class="d-fill-soft" x="34" y="64" width="72" height="72" rx="4"/>
  <circle class="d-node d-role-output" cx="92.0" cy="100.0" r="2.2"/><circle class="d-node d-role-output" cx="91.4" cy="104.9" r="2.2"/><circle class="d-node d-role-output" cx="89.8" cy="109.5" r="2.2"/><circle class="d-node d-role-output" cx="87.2" cy="113.7" r="2.2"/><circle class="d-node d-role-output" cx="83.7" cy="117.2" r="2.2"/><circle class="d-node d-role-output" cx="79.5" cy="119.8" r="2.2"/><circle class="d-node d-role-output" cx="74.9" cy="121.4" r="2.2"/><circle class="d-node d-role-output" cx="70.0" cy="122.0" r="2.2"/><circle class="d-node d-role-output" cx="65.1" cy="121.4" r="2.2"/><circle class="d-node d-role-output" cx="60.5" cy="119.8" r="2.2"/><circle class="d-node d-role-output" cx="56.3" cy="117.2" r="2.2"/><circle class="d-node d-role-output" cx="52.8" cy="113.7" r="2.2"/><circle class="d-node d-role-output" cx="50.2" cy="109.5" r="2.2"/><circle class="d-node d-role-output" cx="48.6" cy="104.9" r="2.2"/><circle class="d-node d-role-output" cx="48.0" cy="100.0" r="2.2"/><circle class="d-node d-role-output" cx="48.6" cy="95.1" r="2.2"/><circle class="d-node d-role-output" cx="50.2" cy="90.5" r="2.2"/><circle class="d-node d-role-output" cx="52.8" cy="86.3" r="2.2"/><circle class="d-node d-role-output" cx="56.3" cy="82.8" r="2.2"/><circle class="d-node d-role-output" cx="60.5" cy="80.2" r="2.2"/><circle class="d-node d-role-output" cx="65.1" cy="78.6" r="2.2"/><circle class="d-node d-role-output" cx="70.0" cy="78.0" r="2.2"/><circle class="d-node d-role-output" cx="74.9" cy="78.6" r="2.2"/><circle class="d-node d-role-output" cx="79.5" cy="80.2" r="2.2"/><circle class="d-node d-role-output" cx="83.7" cy="82.8" r="2.2"/><circle class="d-node d-role-output" cx="87.2" cy="86.3" r="2.2"/><circle class="d-node d-role-output" cx="89.8" cy="90.5" r="2.2"/><circle class="d-node d-role-output" cx="91.4" cy="95.1" r="2.2"/>
  <text class="d-math" x="70" y="154" text-anchor="middle">x<tspan dy="3">0</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-fill-soft" x="164" y="64" width="72" height="72" rx="4"/>
  <circle class="d-node d-role-output" cx="227.9" cy="99.8" r="2.2"/><circle class="d-node d-role-output" cx="219.0" cy="105.0" r="2.2"/><circle class="d-node d-role-output" cx="219.2" cy="111.5" r="2.2"/><circle class="d-node d-role-output" cx="215.3" cy="110.8" r="2.2"/><circle class="d-node d-role-output" cx="210.1" cy="120.4" r="2.2"/><circle class="d-node d-role-output" cx="212.1" cy="122.1" r="2.2"/><circle class="d-node d-role-output" cx="203.7" cy="121.3" r="2.2"/><circle class="d-node d-role-output" cx="204.4" cy="119.9" r="2.2"/><circle class="d-node d-role-output" cx="197.1" cy="123.0" r="2.2"/><circle class="d-node d-role-output" cx="191.9" cy="116.9" r="2.2"/><circle class="d-node d-role-output" cx="189.4" cy="117.5" r="2.2"/><circle class="d-node d-role-output" cx="180.2" cy="116.1" r="2.2"/><circle class="d-node d-role-output" cx="179.8" cy="107.3" r="2.2"/><circle class="d-node d-role-output" cx="181.9" cy="104.4" r="2.2"/><circle class="d-node d-role-output" cx="175.6" cy="103.2" r="2.2"/><circle class="d-node d-role-output" cx="180.6" cy="96.3" r="2.2"/><circle class="d-node d-role-output" cx="182.3" cy="86.0" r="2.2"/><circle class="d-node d-role-output" cx="184.7" cy="86.8" r="2.2"/><circle class="d-node d-role-output" cx="188.7" cy="82.5" r="2.2"/><circle class="d-node d-role-output" cx="192.0" cy="79.3" r="2.2"/><circle class="d-node d-role-output" cx="193.5" cy="80.5" r="2.2"/><circle class="d-node d-role-output" cx="201.1" cy="76.5" r="2.2"/><circle class="d-node d-role-output" cx="203.2" cy="79.2" r="2.2"/><circle class="d-node d-role-output" cx="216.2" cy="80.9" r="2.2"/><circle class="d-node d-role-output" cx="215.7" cy="81.2" r="2.2"/><circle class="d-node d-role-output" cx="218.8" cy="90.1" r="2.2"/><circle class="d-node d-role-output" cx="220.2" cy="91.7" r="2.2"/><circle class="d-node d-role-output" cx="225.6" cy="92.8" r="2.2"/>
  <text class="d-math" x="200" y="154" text-anchor="middle">x<tspan dy="3">1</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-fill-soft" x="344" y="64" width="72" height="72" rx="4"/>
  <circle class="d-node d-role-output" cx="411.0" cy="99.6" r="2.2"/><circle class="d-node d-role-output" cx="393.7" cy="104.8" r="2.2"/><circle class="d-node d-role-output" cx="396.9" cy="113.9" r="2.2"/><circle class="d-node d-role-output" cx="391.1" cy="105.2" r="2.2"/><circle class="d-node d-role-output" cx="383.4" cy="124.4" r="2.2"/><circle class="d-node d-role-output" cx="395.5" cy="124.3" r="2.2"/><circle class="d-node d-role-output" cx="381.5" cy="119.7" r="2.2"/><circle class="d-node d-role-output" cx="391.3" cy="115.1" r="2.2"/><circle class="d-node d-role-output" cx="380.6" cy="124.1" r="2.2"/><circle class="d-node d-role-output" cx="374.9" cy="111.0" r="2.2"/><circle class="d-node d-role-output" cx="375.3" cy="116.8" r="2.2"/><circle class="d-node d-role-output" cx="357.3" cy="119.0" r="2.2"/><circle class="d-node d-role-output" cx="360.4" cy="103.2" r="2.2"/><circle class="d-node d-role-output" cx="368.5" cy="103.3" r="2.2"/><circle class="d-node d-role-output" cx="353.2" cy="108.3" r="2.2"/><circle class="d-node d-role-output" cx="365.3" cy="98.6" r="2.2"/><circle class="d-node d-role-output" cx="367.0" cy="79.6" r="2.2"/><circle class="d-node d-role-output" cx="368.8" cy="88.6" r="2.2"/><circle class="d-node d-role-output" cx="373.5" cy="83.2" r="2.2"/><circle class="d-node d-role-output" cx="375.1" cy="79.1" r="2.2"/><circle class="d-node d-role-output" cx="371.2" cy="85.1" r="2.2"/><circle class="d-node d-role-output" cx="382.8" cy="75.6" r="2.2"/><circle class="d-node d-role-output" cx="380.1" cy="81.5" r="2.2"/><circle class="d-node d-role-output" cx="406.1" cy="83.3" r="2.2"/><circle class="d-node d-role-output" cx="397.8" cy="79.9" r="2.2"/><circle class="d-node d-role-output" cx="400.1" cy="97.0" r="2.2"/><circle class="d-node d-role-output" cx="399.6" cy="94.2" r="2.2"/><circle class="d-node d-role-output" cx="410.7" cy="89.4" r="2.2"/>
  <text class="d-math" x="380" y="154" text-anchor="middle">x<tspan dy="3">t−1</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-fill-soft" x="504" y="64" width="72" height="72" rx="4"/>
  <circle class="d-node d-role-output" cx="571.0" cy="99.4" r="2.2"/><circle class="d-node d-role-output" cx="548.8" cy="104.4" r="2.2"/><circle class="d-node d-role-output" cx="554.2" cy="114.9" r="2.2"/><circle class="d-node d-role-output" cx="547.3" cy="100.6" r="2.2"/><circle class="d-node d-role-output" cx="538.1" cy="126.0" r="2.2"/><circle class="d-node d-role-output" cx="557.3" cy="124.6" r="2.2"/><circle class="d-node d-role-output" cx="539.7" cy="117.3" r="2.2"/><circle class="d-node d-role-output" cx="556.0" cy="110.6" r="2.2"/><circle class="d-node d-role-output" cx="543.3" cy="123.6" r="2.2"/><circle class="d-node d-role-output" cx="537.5" cy="105.8" r="2.2"/><circle class="d-node d-role-output" cx="540.0" cy="115.4" r="2.2"/><circle class="d-node d-role-output" cx="516.3" cy="120.2" r="2.2"/><circle class="d-node d-role-output" cx="522.0" cy="99.8" r="2.2"/><circle class="d-node d-role-output" cx="534.3" cy="102.3" r="2.2"/><circle class="d-node d-role-output" cx="512.9" cy="111.7" r="2.2"/><circle class="d-node d-role-output" cx="529.7" cy="100.4" r="2.2"/><circle class="d-node d-role-output" cx="531.3" cy="75.9" r="2.2"/><circle class="d-node d-role-output" cx="532.6" cy="90.6" r="2.2"/><circle class="d-node d-role-output" cx="537.5" cy="84.7" r="2.2"/><circle class="d-node d-role-output" cx="537.8" cy="80.2" r="2.2"/><circle class="d-node d-role-output" cx="529.9" cy="89.5" r="2.2"/><circle class="d-node d-role-output" cx="543.9" cy="76.3" r="2.2"/><circle class="d-node d-role-output" cx="537.7" cy="84.4" r="2.2"/><circle class="d-node d-role-output" cx="571.0" cy="86.1" r="2.2"/><circle class="d-node d-role-output" cx="558.5" cy="80.0" r="2.2"/><circle class="d-node d-role-output" cx="560.0" cy="102.5" r="2.2"/><circle class="d-node d-role-output" cx="557.9" cy="96.5" r="2.2"/><circle class="d-node d-role-output" cx="571.0" cy="87.5" r="2.2"/>
  <text class="d-math" x="540" y="154" text-anchor="middle">x<tspan dy="3">t</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-fill-soft" x="714" y="64" width="72" height="72" rx="4"/>
  <circle class="d-node d-role-output" cx="781.0" cy="99.1" r="2.2"/><circle class="d-node d-role-output" cx="737.7" cy="101.0" r="2.2"/><circle class="d-node d-role-output" cx="748.2" cy="111.9" r="2.2"/><circle class="d-node d-role-output" cx="740.5" cy="84.2" r="2.2"/><circle class="d-node d-role-output" cx="730.1" cy="120.1" r="2.2"/><circle class="d-node d-role-output" cx="765.6" cy="114.7" r="2.2"/><circle class="d-node d-role-output" cx="743.6" cy="101.1" r="2.2"/><circle class="d-node d-role-output" cx="775.3" cy="89.8" r="2.2"/><circle class="d-node d-role-output" cx="761.2" cy="111.1" r="2.2"/><circle class="d-node d-role-output" cx="757.7" cy="84.9" r="2.2"/><circle class="d-node d-role-output" cx="766.9" cy="103.2" r="2.2"/><circle class="d-node d-role-output" cx="733.6" cy="115.1" r="2.2"/><circle class="d-node d-role-output" cx="745.9" cy="88.0" r="2.2"/><circle class="d-node d-role-output" cx="767.3" cy="97.6" r="2.2"/><circle class="d-node d-role-output" cx="734.0" cy="118.5" r="2.2"/><circle class="d-node d-role-output" cx="760.0" cy="106.6" r="2.2"/><circle class="d-node d-role-output" cx="760.5" cy="73.5" r="2.2"/><circle class="d-node d-role-output" cx="759.4" cy="101.9" r="2.2"/><circle class="d-node d-role-output" cx="762.9" cy="96.8" r="2.2"/><circle class="d-node d-role-output" cx="758.2" cy="93.0" r="2.2"/><circle class="d-node d-role-output" cx="740.1" cy="109.6" r="2.2"/><circle class="d-node d-role-output" cx="756.2" cy="89.4" r="2.2"/><circle class="d-node d-role-output" cx="740.4" cy="101.6" r="2.2"/><circle class="d-node d-role-output" cx="781.0" cy="102.2" r="2.2"/><circle class="d-node d-role-output" cx="762.4" cy="89.5" r="2.2"/><circle class="d-node d-role-output" cx="760.6" cy="120.7" r="2.2"/><circle class="d-node d-role-output" cx="754.1" cy="106.1" r="2.2"/><circle class="d-node d-role-output" cx="775.8" cy="86.2" r="2.2"/>
  <text class="d-math" x="750" y="154" text-anchor="middle">x<tspan dy="3">T</tspan><tspan dy="-3"> </tspan></text>
  <text class="d-title" x="290" y="104" text-anchor="middle">⋯</text>
  <text class="d-title" x="645" y="104" text-anchor="middle">⋯</text>
  <path class="d-line-strong" d="M110 90 H160" marker-end="url(#diffusion-arrow)"/>
  <path class="d-line-accent" d="M160 110 H110" marker-end="url(#diffusion-arrow-accent)"/>
  <path class="d-line-strong" d="M240 90 H274" marker-end="url(#diffusion-arrow)"/>
  <path class="d-line-accent" d="M274 110 H240" marker-end="url(#diffusion-arrow-accent)"/>
  <path class="d-line-strong" d="M306 90 H340" marker-end="url(#diffusion-arrow)"/>
  <path class="d-line-accent" d="M340 110 H306" marker-end="url(#diffusion-arrow-accent)"/>
  <path class="d-line-strong" d="M420 90 H500" marker-end="url(#diffusion-arrow)"/>
  <path class="d-line-accent" d="M500 110 H420" marker-end="url(#diffusion-arrow-accent)"/>
  <path class="d-line-strong" d="M580 90 H629" marker-end="url(#diffusion-arrow)"/>
  <path class="d-line-accent" d="M629 110 H580" marker-end="url(#diffusion-arrow-accent)"/>
  <path class="d-line-strong" d="M661 90 H710" marker-end="url(#diffusion-arrow)"/>
  <path class="d-line-accent" d="M710 110 H661" marker-end="url(#diffusion-arrow-accent)"/>
  <text class="d-math" x="460" y="82" text-anchor="middle">q(x<tspan dy="3">t</tspan><tspan dy="-3"> | x</tspan><tspan dy="3">t−1</tspan><tspan dy="-3">)</tspan></text>
  <text class="d-math" x="460" y="130" text-anchor="middle">p<tspan dy="3">θ</tspan><tspan dy="-3">(x</tspan><tspan dy="3">t−1</tspan><tspan dy="-3"> | x</tspan><tspan dy="3">t</tspan><tspan dy="-3">)</tspan></text>
  <path class="d-line-accent" d="M816 186 H44" marker-end="url(#diffusion-arrow-accent)"/>
  <text class="d-label" x="430" y="204" text-anchor="middle">Reverse process p<tspan dy="3">θ</tspan><tspan dy="-3">: learned; sampling starts from x</tspan><tspan dy="3">T</tspan><tspan dy="-3"> ~ N(0, I) and removes a little noise per step</tspan></text>
  <rect class="d-frame" x="24" y="226" width="812" height="276" rx="6"/>
  <text class="d-title" x="40" y="248" text-anchor="start">Training: one step, any t</text>
  <rect class="d-box d-role-input" x="40" y="270" width="100" height="30" rx="5"/>
  <text class="d-math" x="90" y="289" text-anchor="middle">x<tspan dy="3">0</tspan><tspan dy="-3"> </tspan></text>
  <rect class="d-box d-role-position" x="40" y="330" width="100" height="30" rx="5"/>
  <text class="d-math" x="90" y="349" text-anchor="middle">t ~ U{1, …, T}</text>
  <rect class="d-box d-role-router" x="40" y="390" width="100" height="30" rx="5"/>
  <text class="d-math" x="90" y="409" text-anchor="middle">ε ~ N(0, I)</text>
  <rect class="d-box" x="180" y="268" width="196" height="44" rx="5"/>
  <text class="d-math" x="278" y="287" text-anchor="middle">x<tspan dy="3">t</tspan><tspan dy="-3"> = √ᾱ</tspan><tspan dy="3">t</tspan><tspan dy="-3"> x</tspan><tspan dy="3">0</tspan><tspan dy="-3"> + √(1−ᾱ</tspan><tspan dy="3">t</tspan><tspan dy="-3">) ε</tspan></text>
  <text class="d-note" x="278" y="303" text-anchor="middle">noise to step t in one jump</text>
  <path class="d-line-strong" d="M140 285 H176" marker-end="url(#diffusion-arrow)"/>
  <path class="d-line-strong" d="M140 405 H220 V316" marker-end="url(#diffusion-arrow)"/>
  <text class="d-math" x="517" y="266" text-anchor="middle">U-Net  ε<tspan dy="3">θ</tspan><tspan dy="-3">(x</tspan><tspan dy="3">t</tspan><tspan dy="-3">, t)</tspan></text>
  <path class="d-line-strong" d="M376 290 H416" marker-end="url(#diffusion-arrow)"/>
  <path class="d-line-strong" d="M437 302 L475 318"/>
  <path class="d-line-strong" d="M475 342 L517 358"/>
  <path class="d-line-strong" d="M517 358 L559 342"/>
  <path class="d-line-strong" d="M559 318 L597 302"/>
  <rect class="d-box d-role-feedforward" x="420" y="278" width="34" height="24" rx="5"/>
  <rect class="d-box d-role-feedforward" x="458" y="318" width="34" height="24" rx="5"/>
  <rect class="d-box d-role-feedforward" x="500" y="358" width="34" height="24" rx="5"/>
  <rect class="d-box d-role-feedforward" x="542" y="318" width="34" height="24" rx="5"/>
  <rect class="d-box d-role-feedforward" x="580" y="278" width="34" height="24" rx="5"/>
  <path class="d-line-back" d="M454 290 H577" marker-end="url(#diffusion-arrow-thin)"/>
  <path class="d-line-back" d="M492 330 H539" marker-end="url(#diffusion-arrow-thin)"/>
  <text class="d-note" x="517" y="400" text-anchor="middle">skip connections dashed</text>
  <path class="d-line" d="M140 345 H160 V452 H640 V340 H580" marker-end="url(#diffusion-arrow-thin)"/>
  <text class="d-note" x="400" y="446" text-anchor="middle">time step t (sinusoidal embedding) enters every residual block</text>
  <path class="d-line-strong" d="M614 290 H656" marker-end="url(#diffusion-arrow)"/>
  <rect class="d-box d-role-attention" x="660" y="276" width="50" height="28" rx="5"/>
  <text class="d-math" x="685" y="294" text-anchor="middle">ε̂</text>
  <path class="d-line-strong" d="M710 290 H728" marker-end="url(#diffusion-arrow)"/>
  <rect class="d-box d-role-router" x="732" y="270" width="92" height="40" rx="5"/>
  <text class="d-title" x="778" y="288" text-anchor="middle">‖ε − ε̂‖²</text>
  <text class="d-note" x="778" y="302" text-anchor="middle">squared error</text>
  <path class="d-line-accent" d="M90 420 V478 H778 V314" marker-end="url(#diffusion-arrow-accent)"/>
  <text class="d-note" x="434" y="494" text-anchor="middle">target: the noise ε that was actually mixed in</text>
  </svg>
diagram_caption: "The fixed forward process adds Gaussian noise step by step until x₀ becomes pure noise x_T; the learned reverse process removes it. In training, a U-Net sees xₜ and t and predicts the added noise ε, scored by ‖ε − ε̂‖²."
notable_models:
  - { name: "DDPM", year: 2020, note: "Trained a U-Net to predict the added noise with a squared-error objective; FID 3.17 on CIFAR-10 and LSUN sample quality similar to ProgressiveGAN." }
  - { name: "Latent Diffusion / Stable Diffusion", year: 2021, note: "Runs diffusion in the latent space of a pre-trained autoencoder to cut compute, with cross-attention for text and other inputs; Stable Diffusion v1-4 is a latent text-to-image diffusion model of this kind." }
  - { name: "Imagen", year: 2022, note: "Text-to-image diffusion with a frozen T5-XXL text encoder trained on text only; its authors found that scaling the text encoder improved sample quality more than scaling the image diffusion model, and report a zero-shot COCO FID of 7.27." }
  - { name: "DALL·E 2", year: 2022, note: "Two stages: a prior turns a caption into a CLIP image embedding, and a diffusion decoder generates the image from that embedding." }

resources:
  - { kind: paper, title: "Denoising Diffusion Probabilistic Models (Ho, Jain & Abbeel, 2020)", url: "https://arxiv.org/abs/2006.11239" }
  - { kind: blog, title: "What are Diffusion Models? (Lilian Weng)", url: "https://lilianweng.github.io/posts/2021-07-11-diffusion-models/" }
  - { kind: tutorial, title: "The Annotated Diffusion Model (Hugging Face)", url: "https://huggingface.co/blog/annotated-diffusion" }
  - { kind: paper, title: "High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2021)", url: "https://arxiv.org/abs/2112.10752" }

learn_first:
  - { label: "Variational autoencoders (latent-variable view)", href: "/models/architecture/vae", tag: "Architecture" }
  - { label: "The Gaussian & adding noise", href: "/learn/concepts/gaussian", tag: "Math" }

tags: [diffusion, generation, denoising, text-to-image]
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/1503.03585", title: "Deep Unsupervised Learning using Nonequilibrium Thermodynamics (Sohl-Dickstein et al., 2015)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2006.11239", title: "Denoising Diffusion Probabilistic Models (Ho, Jain & Abbeel, 2020)", trust: 0.98 }
  - { url: "https://arxiv.org/abs/2207.12598", title: "Classifier-Free Diffusion Guidance (Ho and Salimans, 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2205.11487", title: "Photorealistic Text-to-Image Diffusion Models with Deep Language Understanding (Imagen, Saharia et al., 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2204.06125", title: "Hierarchical Text-Conditional Image Generation with CLIP Latents (DALL·E 2, Ramesh et al., 2022)", trust: 0.95 }
  - { url: "https://huggingface.co/CompVis/stable-diffusion-v1-4", title: "CompVis/stable-diffusion-v1-4 model card (Hugging Face)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2112.10752", title: "High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2021)", trust: 0.95 }
---

## What Changed

The table above compares diffusion models with **generative adversarial networks (GANs)**, as the
latent diffusion authors describe both. In a GAN, a generator network makes an image while a
second network, the discriminator, is trained to tell its images from real ones.

How to read it:

- **Likelihood-based** means the model is trained to give high probability to the real training
  data, rather than to fool a second network.
- **Mode collapse** is a GAN failure in which the generator keeps producing only a few kinds of
  output and ignores the rest of the data.
- **Mode-covering** is the opposite habit: the model tries to cover all of the data, including
  fine details nobody can see, which costs compute.
- **The trade:** diffusion models train more steadily and cover the data better, but make each
  sample with many network passes instead of one.

## Key Ideas

- **Forward noising is fixed.** A set schedule buries an image under Gaussian noise over T steps;
  nothing in it is learned.
- **Learned denoising.** One network, shared by all steps, predicts the noise in a noisy image.
- **Jump to any step.** Training never runs the whole chain: it noises an image straight to a
  random step and trains on that one prediction.
- **Many steps to sample.** Generation starts from pure noise and runs the network once per step.
- **Later work:** latent diffusion works in an autoencoder's smaller latent space and adds
  cross-attention for text; classifier-free guidance pushes samples toward their condition.

## How It Works

The figure above has two halves. The top row is the **chain** of noisy versions of one example,
from x₀ (clean, left) to x_T (pure noise, right); the box below is **one training step**.

**Gaussian noise** is random numbers drawn from a bell curve centred on 0. **x_t** is the image after
t noising steps; in DDPM, images have pixel values scaled to the range −1 to 1.

1. **Forward process (top arrow, left to right).** Each step shrinks the image slightly and adds a
   little Gaussian noise: x_t is drawn from N(√(1−β_t)·x_{t−1}, β_t·I). DDPM uses **T = 1000** steps
   with β_t rising linearly from **0.0001** to **0.02**. This process is fixed; nothing is learned.
2. **Jump straight to step t.** Multiplying the shrink factors gives ᾱ_t = (1−β₁)(1−β₂)…(1−β_t),
   and x_t = √ᾱ_t·x₀ + √(1−ᾱ_t)·ε with ε standard Gaussian noise. With DDPM's schedule,
   √ᾱ_t is about 0.72 at t = 250, 0.28 at t = 500 and 0.006 at t = 1000, so x_1000 is almost pure
   noise (computed from the schedule).
3. **Training inputs (left of the box).** Take a training image x₀, a step t drawn uniformly from
   1 to 1000, and fresh noise ε. Build x_t with the formula in step 2.
4. **U-Net ε_θ(x_t, t).** The noisy image goes into a **U-Net**, a convolutional network that
   works at several resolutions (32 × 32 down to 4 × 4 for DDPM's CIFAR-10 model), with **skip
   connections** (dashed) joining matching sizes. DDPM adds self-attention at the 16 × 16 resolution, and gives the step t
   to every residual block as a sinusoidal embedding, so one network serves all 1000 steps. Its
   output ε̂ is a guess of the noise, the same size as the image.
5. **Loss ‖ε − ε̂‖² (right of the box).** The squared difference between the real noise and the
   guess, summed over pixels. A gradient step lowers it, and training repeats with a new image, t
   and ε (the paper's Algorithm 1).
6. **Reverse process (lower arrow, right to left): sampling.** Start from x_T, pure noise. For t
   from 1000 down to 1, compute x_{t−1} = (1/√α_t)·(x_t − (β_t/√(1−ᾱ_t))·ε_θ(x_t, t)) + σ_t·z, with
   α_t = 1 − β_t, fresh noise z, and σ_t² = β_t (the paper found a second choice of σ_t gave similar
   results). At t = 1 no noise is added. That is **1000 network passes** for one image.
7. **Later: latent diffusion.** Latent diffusion first trains an autoencoder, then runs steps 1
   to 6 on its smaller latent instead of the pixels. Stable Diffusion v1-4 turns a 512 × 512 × 3
   image into a 64 × 64 × 4 latent and feeds the text encoder's output into the U-Net through
   **cross-attention**.
8. **Later: classifier-free guidance.** One network learns both with and without the condition:
   during training the condition (a class or a caption) is replaced by a null token with
   probability p_uncond (0.1 and 0.2 worked about equally in the paper). At sampling time the two
   guesses are mixed as ε̃ = (1 + w)·ε_θ(z, c) − w·ε_θ(z), where w sets the guidance strength.

## Worked Example

**One training step and one sampling step by hand**, for a single pixel. The numbers are an
example, not from a trained model, chosen so the square roots come out round; a real CIFAR-10
image has 32 × 32 × 3 = 3072 such numbers, each handled the same way.

The clean pixel is **x₀ = 0.8**. Take a step t where **ᾱ_t = 0.64**, so √ᾱ_t = **0.8** and
√(1 − ᾱ_t) = √0.36 = **0.6**. For comparison, DDPM's real schedule reaches ᾱ_t ≈ 0.64 near
t = 207, where β_t ≈ 0.004 (computed from the schedule).

**Training step**

1. **Draw the noise:** suppose ε = **−0.5**.
2. **Make the noisy pixel:** x_t = 0.8 × 0.8 + 0.6 × (−0.5) = 0.64 − 0.30 = **0.34**.
3. **The network guesses the noise:** suppose it returns ε̂ = **−0.4**.
4. **Score the guess:** (ε − ε̂)² = (−0.5 − (−0.4))² = (−0.1)² = **0.01**. Training lowers this
   number by adjusting the network's weights.
5. **What the guess implies about the clean pixel** (Eq. 15 of the paper):
   x̂₀ = (x_t − √(1−ᾱ_t)·ε̂) / √ᾱ_t = (0.34 − 0.6 × (−0.4)) / 0.8 = (0.34 + 0.24) / 0.8 = 0.58 / 0.8 =
   **0.725**. The true value is 0.8: a better noise guess gives a better clean estimate.

**Sampling step** (the paper's Algorithm 2), from the same x_t = 0.34 and ε̂ = −0.4, with
**β_t = 0.02** (so α_t = 0.98; larger than the real schedule's value at this step, to keep the
numbers readable) and fresh noise **z = 0.1**:

1. **Noise coefficient:** β_t / √(1−ᾱ_t) = 0.02 / 0.6 ≈ **0.0333**.
2. **Remove the predicted noise:** x_t − 0.0333 × (−0.4) = 0.34 + 0.0133 = **0.3533**.
3. **Undo the shrink:** divide by √α_t = √0.98 ≈ 0.98995: 0.3533 / 0.98995 ≈ **0.3569**.
4. **Add a little fresh noise:** σ_t = √β_t = √0.02 ≈ 0.1414, and 0.1414 × 0.1 ≈ 0.0141.
   x_{t−1} ≈ 0.3569 + 0.0141 = **0.3711**.

**Guidance, one line:** if the guess with the caption were −0.4 and without it −0.1, guidance
strength w = 3 would use (1 + 3) × (−0.4) − 3 × (−0.1) = −1.6 + 0.3 = **−1.3**, pushing further in
the direction the caption suggests.

**What this means:** the network never draws an image directly; it only guesses noise. Training
checks each guess against the noise that was really added, and sampling subtracts a small part of
the guess, 1000 times in DDPM, until pure noise has become a new image.
