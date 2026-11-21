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
description: "Generative models that learn to reverse a gradual noising process and generate data by iterative denoising."
key_features: ["Fixed forward process that gradually adds noise", "Learned reverse (denoising) process", "Sampling by many sequential denoising steps"]
key_models: ["DDPM (2020)", "Stable Diffusion / LDM (2022)", "Imagen (2022)"]
paper_title: "Deep Unsupervised Learning using Nonequilibrium Thermodynamics (Sohl-Dickstein et al., 2015)"
paper_url: "https://arxiv.org/abs/1503.03585"
influenced_by: [architecture-era/vae, architecture-era/gan]
led_to: []

eli5: "A diffusion model learns to un-blur noise into a picture. First you take real images and slowly add TV static until they are pure noise. Then you train a network to undo one tiny step of that. To create something new, start from random static and let the network clean it up, step by step, until a fresh image appears."

analogy:
  title: "Sculpting a statue out of static"
  body: "A sculptor starts with a rough block and chips away, a little at a time, until a figure emerges. Diffusion works the same way, but the block is a screen of random static and the chisel is a denoiser. The model learned by watching the reverse: a clean photo buried under static one thin layer at a time, with a record of exactly what static was added at each step. To make a new image, hand the model fresh static and let it remove the noise step by step."

narrative:
  motivation: "A generative model must be flexible enough to fit complex data yet tractable to train, sample from and evaluate. Sohl-Dickstein et al. (2015) proposed reaching both by splitting generation into many small steps."
  approach: "Define a fixed forward diffusion process that slowly destroys structure in the data by adding noise over many steps, then learn a reverse diffusion process that restores it. Each learned step only has to undo a small amount of noise."
  solution: "DDPM (Ho, Jain and Abbeel, 2020) adds Gaussian noise under a variance schedule over T = 1000 steps and trains a U-Net to predict the noise mixed into a noisy image, with a simple squared-error objective. To generate, start from pure noise and apply the learned denoiser step by step back to a clean sample."
  outcome: "DDPM reports a Fréchet Inception Distance (FID) of 3.17 on CIFAR-10 and LSUN 256×256 sample quality similar to ProgressiveGAN. Latent diffusion (Rombach et al., 2022) runs the process in the latent space of a pretrained autoencoder to cut compute, and adds cross-attention for conditioning on inputs such as text."

build_steps:
  - { title: "1. Define the forward noising schedule", detail: "Fix how much Gaussian noise to add at each of many timesteps. Nothing is learned here; the process gradually turns any real image into pure noise." }
  - { title: "2. Make a noisy training example", detail: "Take a real image, pick a random timestep, and jump straight to that noise level in one step (the Gaussian forward process allows this in closed form). Keep the exact noise that was added: it is the target." }
  - { title: "3. Predict the noise", detail: "Feed the noisy image and the timestep into the network (a U-Net in DDPM), which predicts the noise that was added." }
  - { title: "4. Train on the error", detail: "Minimise the squared difference between the predicted and the real noise, across many images, timesteps and noise draws." }
  - { title: "5. Sample by denoising from noise", detail: "To generate, start from pure random noise and use the trained network to remove a little noise at each step, walking backward through the schedule to a clean image." }
  - { title: "6. Steer with guidance (optional)", detail: "Classifier-free guidance (Ho and Salimans) trains a conditional and an unconditional model jointly and combines their estimates, trading diversity for sample quality." }

key_equation:
  latex: "L = \\mathbb{E}_{t,\\,x_0,\\,\\epsilon}\\left[\\; \\bigl\\lVert \\epsilon - \\epsilon_\\theta\\!\\left(\\sqrt{\\bar{\\alpha}_t}\\,x_0 + \\sqrt{1-\\bar{\\alpha}_t}\\,\\epsilon,\\; t\\right) \\bigr\\rVert^2 \\;\\right]"
  plain: "DDPM's simplified objective: take a clean image, mix in a known amount of random noise to land at timestep t, and ask the network to predict that noise. The loss is the squared difference between the real noise and the prediction."
  terms:
    - { symbol: "x₀", meaning: "a clean training image (before any noise is added)" }
    - { symbol: "ε", meaning: "the random Gaussian noise actually mixed in — the target the network must predict" }
    - { symbol: "ε_θ(·)", meaning: "the network's predicted noise, given the noisy image and timestep" }
    - { symbol: "t", meaning: "the timestep — how far along the noising schedule this example is" }
    - { symbol: "ᾱ_t", meaning: "the schedule's cumulative signal level at step t; √ᾱ_t scales the image and √(1−ᾱ_t) scales the noise" }

strengths:
  - "Trained with a simple squared-error objective on the noise, with no adversary."
  - "High sample quality: DDPM reports FID 3.17 on CIFAR-10 and LSUN quality similar to ProgressiveGAN."
  - "Conditioning on inputs such as text or bounding boxes through cross-attention (latent diffusion)."
  - "Guidance can be applied without retraining the model, per the latent diffusion paper."

weaknesses:
  - "Slow sampling: generating one image needs many sequential network evaluations."
  - "Expensive in pixel space: the latent diffusion authors note training can take hundreds of GPU days."
  - "Guidance trades sample diversity for quality."

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
  <text class="d-note" x="400" y="446" text-anchor="middle">time step t (embedding) conditions every block</text>
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
  - { name: "DDPM", year: 2020, note: "Trained a U-Net to predict added noise with a squared-error objective; reported sample quality on LSUN similar to ProgressiveGAN." }
  - { name: "Latent Diffusion / Stable Diffusion", year: 2022, note: "Runs diffusion in the latent space of a pretrained autoencoder to reduce compute; Stable Diffusion is a latent text-to-image diffusion model." }
  - { name: "Imagen", year: 2022, note: "Text-to-image diffusion with a frozen text-only language model (T5) as encoder; its authors found scaling the text encoder helped more than scaling the diffusion model." }
  - { name: "DALL·E 2", year: 2022, note: "A prior produces a CLIP image embedding from a caption and a diffusion decoder generates the image from it." }

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
updated: '2026-11-21'
sources:
  - { url: "https://arxiv.org/abs/1503.03585", title: "Deep Unsupervised Learning using Nonequilibrium Thermodynamics (Sohl-Dickstein et al., 2015)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2006.11239", title: "Denoising Diffusion Probabilistic Models (Ho, Jain & Abbeel, 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2207.12598", title: "Classifier-Free Diffusion Guidance (Ho and Salimans, 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2205.11487", title: "Photorealistic Text-to-Image Diffusion Models with Deep Language Understanding (Imagen, Saharia et al., 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2204.06125", title: "Hierarchical Text-Conditional Image Generation with CLIP Latents (DALL·E 2, Ramesh et al., 2022)", trust: 0.95 }
  - { url: "https://huggingface.co/CompVis/stable-diffusion-v1-4", title: "CompVis/stable-diffusion-v1-4 model card (Hugging Face)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/2112.10752", title: "High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., 2022)", trust: 0.95 }
  - { url: "https://lilianweng.github.io/posts/2021-07-11-diffusion-models/", title: "What are Diffusion Models? (Lilian Weng)", trust: 0.9 }
---

## What Changed
Diffusion models (Sohl-Dickstein et al., 2015) split generation into many small steps.

- The **forward process** adds noise to a real image over many steps until only noise remains; it is
  fixed, with nothing learned.
- A network learns the **reverse process**: in DDPM (Ho et al., 2020) it predicts the noise at each
  step, trained with a squared-error objective.
- Sampling starts from pure noise and denoises step by step to a new image.

## Key Ideas
- **Forward noising**: a fixed schedule gradually corrupts data into Gaussian noise, defining the
  targets the model learns to reverse.
- **Learned denoising**: the network predicts the noise at each timestep; sampling chains many such
  predictions from noise back to a clean sample.
- **Many steps at inference**: each step is simple to learn, but sampling needs many sequential
  network evaluations.
- **Latent diffusion and guidance**: later work diffuses in an autoencoder's latent space to cut
  compute, and classifier-free guidance trades diversity for sample quality.
