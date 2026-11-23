---
id: architecture-era/vae
type: architecture-era
slug: vae
schema_version: 2.5
title: "Variational Autoencoder"
name: "Variational Autoencoder"
era: foundation
era_range: "2013–2020"
year: 2013
family: generation
core_innovation: "A probabilistic encoder-decoder trained by variational inference with the reparameterization trick, learning a continuous latent space."
description: "A generative model made of two networks: an encoder that turns a data point into a small cloud of likely codes, and a decoder that turns a code back into data; both are trained together, and new data is made by decoding random codes."
key_features: ["Probabilistic encoder (approximate posterior)", "Variational lower bound objective", "Reparameterization trick"]
key_models: ["VAE (2013)", "VQ-VAE (2017)", "NVAE (2020)"]
paper_title: "Auto-Encoding Variational Bayes (Kingma & Welling, 2013)"
paper_url: "https://arxiv.org/abs/1312.6114"
influenced_by: []
led_to: [architecture-era/diffusion]

analogy:
  title: "A recipe card that is a little blurry on purpose"
  body: "This is an analogy, not the mechanism. One cook writes any dish as a short recipe card, and a second cook rebuilds the dish from the card alone. The first cook writes each quantity as a range, and a random draw within the range is what the second cook gets, so neighbouring cards must give similar dishes. A house rule also asks for ranges near the usual ones. Then a card drawn at random still rebuilds a sensible dish. The cards are codes, the cooks are the encoder and decoder, the house rule is the KL term, and the exact rule is the key equation below."

narrative:
  motivation: "**Learning with a hidden cause that cannot be worked out exactly.** The question is posed by **Kingma and Welling (2013)**.\n\n- **The setting:** data generated from an unobserved continuous variable z, the **latent variable**, through a model with unknown parameters.\n- **Intractable:** for a model such as a neural network with a nonlinear hidden layer, the likelihood of the data and the posterior of z given x **cannot be computed**, so the EM algorithm and mean-field variational methods fail.\n- **Large datasets:** batch optimisation and per-example sampling loops such as Monte Carlo EM are **too slow**.\n- **The obstacle:** the usual Monte Carlo estimate of the gradient of the variational bound has **very high variance**, which the authors call impractical."
  approach: "**An encoder that guesses the hidden cause, and a bound that can be differentiated.**\n\n- **Recognition model:** a network q(z|x) approximates the true posterior; the authors call it a **probabilistic encoder**, and the model p(x|z) a **probabilistic decoder**.\n- **The bound:** the log-likelihood of each data point equals a lower bound **plus** the KL divergence from q(z|x) to the true posterior; since that divergence is never negative, raising the bound pushes up the likelihood.\n- **Two parts:** the bound is a reconstruction term minus the **KL divergence from q(z|x) to the prior p(z)**.\n- **Reparameterisation:** a sample of z is written as a differentiable function of the encoder's outputs and **independent noise** ε, so ordinary stochastic gradients flow through the sampling step."
  solution: "**The variational autoencoder: Gaussian codes, a closed-form KL term, one sample per data point.**\n\n- **Prior:** a standard normal, **N(0, I)**, with no parameters.\n- **Encoder:** a network with one hidden layer outputs a **mean μ** and a **standard deviation σ** for each code dimension.\n- **Sampling:** **z = μ + σ ⊙ ε** with ε drawn from N(0, I); ⊙ multiplies element by element.\n- **KL term in closed form:** for Gaussians, minus the KL divergence is **½ Σ (1 + log σ² − μ² − σ²)**, so only the reconstruction term needs sampling.\n- **Decoder:** a one-hidden-layer network giving a **Bernoulli** (binary data) or **Gaussian** (real-valued data) distribution over x.\n- **Training:** minibatches of **M = 100** data points with **L = 1** sample each, optimised with Adagrad."
  outcome: "**A trainable generative model with a usable code space.**\n\n- **Against wake-sleep:** on MNIST and Frey Face the authors' method converged **considerably faster** and reached a **better bound in all experiments**.\n- **No overfitting from extra codes:** adding latent dimensions, up to **200** on MNIST, did not cause overfitting, which the authors credit to the bound's regularising effect.\n- **Pictures of the code space:** with a **2-dimensional** code, decoding a grid of codes draws a learned map of digits or faces.\n- **Found twice:** the paper notes that **Rezende, Mohamed and Wierstra (2014)** made the same connection independently.\n- **Descendants:** VQ-VAE (2017) uses **discrete** codes and a learnt prior; NVAE (2020) reached **256 × 256** images; latent diffusion (2021) trains its diffusion model in the code space of an autoencoder with a **slight KL penalty towards a standard normal**, similar to a VAE.\n- **Diffusion models:** **Ho et al. (2020)** describe them as a class of **latent variable models** and obtain their best results by training on a **weighted variational bound**."

key_equation:
  latex: "\\mathcal{L}(\\theta, \\phi; x) \\simeq \\underbrace{\\tfrac{1}{2} \\sum_{j=1}^{J} \\left(1 + \\log \\sigma_j^2 - \\mu_j^2 - \\sigma_j^2\\right)}_{-D_{\\mathrm{KL}}(q_\\phi(z \\mid x)\\,\\|\\,p_\\theta(z))} + \\underbrace{\\log p_\\theta(x \\mid z)}_{\\text{reconstruction}}, \\qquad z = \\mu + \\sigma \\odot \\epsilon,\\;\\; \\epsilon \\sim \\mathcal{N}(0, I)"
  plain: "In words: the score to maximise for one data point x has two parts. The first is minus the KL divergence between the encoder's Gaussian and the standard-normal prior, computed exactly from the encoder's means μ and standard deviations σ; it is 0 when every μ is 0 and every σ is 1, and negative otherwise. The second is how probable the decoder makes x, given a code z drawn by adding σ times random noise to μ. This is the paper's equation (10) with one sample, as in its experiments. The worked example above computes both parts by hand."
  terms:
    - { symbol: "x", meaning: "one data point, such as the pixels of an image" }
    - { symbol: "z", meaning: "the code (latent variable) for x, with J dimensions" }
    - { symbol: "μ_j, σ_j", meaning: "the mean and standard deviation of code dimension j, output by the encoder for this x" }
    - { symbol: "ε", meaning: "random noise drawn from a standard normal, independent of the networks" }
    - { symbol: "q_φ(z|x)", meaning: "the encoder: a Gaussian over codes given x, with network weights φ" }
    - { symbol: "p_θ(x|z)", meaning: "the decoder: the probability of x given a code, with network weights θ" }
    - { symbol: "p_θ(z)", meaning: "the prior over codes: a standard normal N(0, I)" }
    - { symbol: "D_KL", meaning: "the Kullback–Leibler (KL) divergence, a measure of how far one distribution is from another; never negative" }

strengths:
  - "**One objective, ordinary training:** the authors' estimator of the lower bound can be differentiated and optimised with **standard stochastic gradient** methods."
  - "**Fast inference for each data point:** the trained encoder gives an approximate posterior in one pass, **without iterative schemes such as MCMC** for each data point, the authors write."
  - "**Extra code dimensions did not overfit:** superfluous latent variables did not cause overfitting in the authors' experiments, which they attribute to the **regularising nature** of the bound."
  - "**Fast sampling, accessible encoder:** the NVAE authors list **fast and tractable sampling** and **easy-to-access encoding networks** as advantages of VAEs over other likelihood-based models."

weaknesses:
  - "**Posterior collapse:** the VQ-VAE authors write that in the VAE framework the latents are typically **ignored** when paired with a powerful **autoregressive decoder**."
  - "**Lower likelihood than rivals (2020):** the NVAE authors write that VAEs were then **outperformed** by normalising flows and autoregressive models."
  - "**A bound, not the likelihood:** the gap between the log-likelihood and the bound is the **KL divergence** from the encoder to the true posterior, and the true posterior is exactly what cannot be computed here."
  - "**Continuous codes only:** the reparameterisation needs continuous latent variables; the authors note that the wake-sleep algorithm, unlike theirs, also applies to **discrete** ones."
  - "**Deep versions are unstable:** the NVAE authors write that training very deep hierarchical VAEs is **often unstable**, because the KL divergence in the bound is **unbounded**."

comparison:
  before: "Earlier approaches"
  after: "Auto-Encoding Variational Bayes (the VAE)"
  rows:
    - { aspect: "Gradient of the bound for the encoder's parameters", before: "The usual Monte Carlo estimator: very high variance, impractical", after: "Reparameterised samples z = μ + σ ⊙ ε give a differentiable estimator", source: "https://arxiv.org/abs/1312.6114" }
    - { aspect: "Objective (wake-sleep algorithm)", before: "Two objective functions optimised together, which do not correspond to a bound on the likelihood", after: "One lower bound, optimised for encoder and decoder jointly", source: "https://arxiv.org/abs/1312.6114" }
    - { aspect: "Inference for each data point (Monte Carlo EM)", before: "An expensive sampling loop per data point; not efficient on the full MNIST set", after: "One pass through the encoder", source: "https://arxiv.org/abs/1312.6114" }
    - { aspect: "Keeping the code useful (autoencoders)", before: "Added regularisers (denoising, contractive, sparse) with a nuisance hyperparameter", after: "The KL term, set by the bound itself, with no such hyperparameter", source: "https://arxiv.org/abs/1312.6114" }
    - { aspect: "Bound reached on MNIST and Frey Face", before: "Wake-sleep: slower, and a worse bound", after: "Considerably faster convergence and a better bound in all experiments", source: "https://arxiv.org/abs/1312.6114" }
    - { aspect: "Discrete latent variables", before: "Wake-sleep handles them", after: "Not covered: the method is for continuous latent variables", source: "https://arxiv.org/abs/1312.6114" }

key_numbers:
  columns: ["Setting (Kingma & Welling)", "MNIST", "Frey Face"]
  rows:
    - { cells: ["Hidden units in the encoder and in the decoder", "500", "200 (a smaller dataset, to prevent overfitting)"], source: "https://arxiv.org/abs/1312.6114" }
    - { cells: ["Code dimensions tried (N_z)", "3, 5, 10, 20, 200", "2, 5, 10, 20"], source: "https://arxiv.org/abs/1312.6114" }
    - { cells: ["Minibatch size M and samples per data point L", "M = 100, L = 1", "M = 100, L = 1"], source: "https://arxiv.org/abs/1312.6114" }
    - { cells: ["Starting weights", "Drawn from N(0, 0.01)", "Drawn from N(0, 0.01)"], source: "https://arxiv.org/abs/1312.6114" }
    - { cells: ["Step size", "Adagrad, global step chosen from 0.01, 0.02 and 0.1", "Adagrad, global step chosen from 0.01, 0.02 and 0.1"], source: "https://arxiv.org/abs/1312.6114" }
    - { cells: ["Marginal-likelihood experiment", "100 hidden units, 3 code dimensions", "not run"], source: "https://arxiv.org/abs/1312.6114" }
    - { cells: ["Compute", "About 20 to 40 minutes per million training samples on an Intel Xeon CPU at an effective 40 GFLOPS", "Same"], source: "https://arxiv.org/abs/1312.6114" }

misunderstandings:
  - misunderstanding: "The KL term is a regulariser added to an autoencoder by hand."
    truth: "It comes out of the **lower bound** itself: the bound equals the expected log-probability of x under the decoder **minus** the KL divergence from q(z|x) to the prior. The authors contrast this with autoencoder regularisers that need a **nuisance hyperparameter**, which their objective lacks."
    source: "https://arxiv.org/abs/1312.6114"
  - misunderstanding: "The encoder outputs a single code for each input."
    truth: "It outputs a **distribution** over codes, in the paper a Gaussian with mean μ and standard deviation σ. A code is drawn as **z = μ + σ ⊙ ε** with noise ε from N(0, I), which keeps the sampling step differentiable with respect to μ and σ."
    source: "https://arxiv.org/abs/1312.6114"
  - misunderstanding: "Kingma and Welling were the only ones to find this method."
    truth: "Their paper states that **Rezende, Mohamed and Wierstra (2014)** made the connection between auto-encoders, directed probabilistic models and stochastic variational inference with the same reparameterisation, **developed independently**."
    source: "https://arxiv.org/abs/1312.6114"
  - misunderstanding: "VQ-VAE is a VAE trained on the same bound with discrete codes."
    truth: "The NVAE authors write that although VQ-VAE is **motivated** by VAEs, its objective **does not correspond to a lower bound** on the data log-likelihood. VQ-VAE's own paper lists its two differences: **discrete** codes and a **learnt** prior rather than a fixed one."
    source: "https://arxiv.org/abs/2007.03898"

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
  - { name: "VQ-VAE", year: 2017, note: "Discrete rather than continuous codes, via vector quantisation, and a learnt prior; with an autoregressive prior it generated images, videos and speech." }
  - { name: "NVAE", year: 2020, note: "A deep hierarchical VAE with state-of-the-art results among non-autoregressive likelihood-based models (2.91 bits per dimension on CIFAR-10); the authors report it as the first successful VAE on natural images as large as 256×256 pixels." }

resources:
  - { kind: paper, title: "Auto-Encoding Variational Bayes (Kingma & Welling, 2013)", url: "https://arxiv.org/abs/1312.6114" }
  - { kind: blog, title: "From Autoencoder to Beta-VAE (Lilian Weng)", url: "https://lilianweng.github.io/posts/2018-08-12-vae/" }
  - { kind: tutorial, title: "Variational Autoencoder tutorial (Keras)", url: "https://keras.io/examples/generative/vae/" }
  - { kind: video, title: "Variational Autoencoders (Arxiv Insights)", url: "https://www.youtube.com/watch?v=9zKuYvjFFS8" }

learn_first:
  - { label: "Probability distributions & the Gaussian", href: "/learn/concepts/gaussian", tag: "Math" }
  - { label: "KL divergence", href: "/learn/concepts/kl-divergence", tag: "Math" }

tags: [vae, generation, latent-variable, variational-inference]
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/1312.6114", title: "Auto-Encoding Variational Bayes (Kingma & Welling, arXiv:1312.6114, 2013)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1711.00937", title: "Neural Discrete Representation Learning (van den Oord, Vinyals & Kavukcuoglu, arXiv:1711.00937, 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2007.03898", title: "NVAE: A Deep Hierarchical Variational Autoencoder (Vahdat & Kautz, arXiv:2007.03898, 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2006.11239", title: "Denoising Diffusion Probabilistic Models (Ho, Jain & Abbeel, arXiv:2006.11239, 2020)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2112.10752", title: "High-Resolution Image Synthesis with Latent Diffusion Models (Rombach et al., arXiv:2112.10752, 2021)", trust: 0.95 }
---

## What Changed

A plain autoencoder squeezes each input into **one code** and rebuilds it. A VAE's encoder gives a
**range of likely codes**, and training rewards both good rebuilding and ranges that stay close to
a simple standard-normal prior.

How to read the table above:

- **Latent variable (code):** a hidden number, or list of numbers, that the model assumes
  produced each data point; it is never observed.
- **Lower bound:** a number that is always at or below the log-likelihood of the data; raising it
  is the training goal, because the likelihood itself cannot be computed.
- **Variance of an estimator:** how much a random estimate jumps around; a gradient estimate with
  very high variance gives steps too noisy to train with.
- **Wake-sleep** and **Monte Carlo EM** are the two earlier methods the paper compares against.

## Key Ideas

- **Probabilistic encoder:** the encoder outputs a mean and a standard deviation for each code
  dimension, not one fixed code.
- **One bound, two parts:** a reconstruction term rewards rebuilding x; a KL term keeps the
  encoder's Gaussian close to the prior N(0, I).
- **Reparameterisation:** a code is the mean plus the standard deviation times outside noise, so
  gradients pass through the random draw.
- **Generation:** after training, codes drawn from N(0, I) and passed through the decoder give new
  data.

## How It Works

The figure above runs from the input x on the left to its reconstruction x̂ on the right; the
two boxes outside the main row are the two parts of the loss.

A **Gaussian** (normal distribution) is the bell curve, set by a mean (its centre) and a standard
deviation (its width). N(0, I) is the standard one: mean 0 and width 1 in every dimension.

1. **Input x.** One data point, such as the pixels of a digit image.
2. **Encoder q_φ(z | x).** A network with weights φ reads x and outputs two lists: a mean **μ**
   and a standard deviation **σ** for each code dimension. In the paper it has one hidden layer;
   the experiments used 500 hidden units on MNIST.
3. **Noise ε ~ N(0, I).** A random draw from the standard normal, one number per code dimension.
   It comes from outside the networks.
4. **Reparameterisation (the boxed ⊙ and +).** The code is z = μ + σ ⊙ ε: each σ times its noise,
   added to its μ. Because ε does not depend on the weights, the change of z with μ is 1 and with
   σ is ε, so gradients reach the encoder.
5. **Latent bottleneck z.** The sampled code, with far fewer numbers than x; the paper tried 2
   to 200 dimensions.
6. **Decoder p_θ(x | z).** A second network with weights θ turns z into a distribution over x:
   for binary pixels, one probability per pixel (Bernoulli); for real values, a Gaussian.
7. **Reconstruction term (bottom box).** −log p_θ(x | z): small when the decoder gives the real x
   a high probability.
8. **KL term (top box).** The divergence between the encoder's Gaussian and N(0, I), computed
   exactly from μ and σ: ½ Σ (μ² + σ² − 1 − log σ²) for the paper's Gaussians.
9. **Loss and training.** The loss is reconstruction plus KL, the negative of the bound. Encoder
   and decoder are updated together by stochastic gradients on minibatches of 100 data points,
   one noise sample each.
10. **Making new data.** Draw z from N(0, I) and run only the decoder.

## Worked Example

**The two parts of the loss for one data point, by hand**, with a 2-number code and a 3-pixel
image. The encoder's outputs, the noise and the decoder's outputs are made up so every step can be
checked: an example, not from a trained model.

The image is x = (1, 0, 1). The encoder returns μ = (0.5, −1.0) and σ = (1.0, 0.5), and the noise
draw is ε = (0.4, −0.2). "log" is the natural logarithm; numbers are rounded to 4 decimals.

1. **Draw the code.** z = μ + σ ⊙ ε:
   - z₁ = 0.5 + 1.0 × 0.4 = **0.9**.
   - z₂ = −1.0 + 0.5 × (−0.2) = **−1.1**.
2. **How z responds to the encoder** (what the reparameterisation buys):
   - Raising μ₁ or μ₂ by a little raises z by the same amount: the rate is **1**.
   - Raising σ₁ raises z₁ at rate ε₁ = **0.4**; raising σ₂ changes z₂ at rate ε₂ = **−0.2**.
3. **KL term, dimension 1** (μ = 0.5, σ = 1.0, log σ² = log 1 = 0):
   - ½ × (1 + 0 − 0.25 − 1) = **−0.125**.
4. **KL term, dimension 2** (μ = −1.0, σ = 0.5, log σ² = log 0.25 = −1.3863):
   - ½ × (1 − 1.3863 − 1 − 0.25) = **−0.8181**.
   - Together: −0.125 − 0.8181 = −0.9431, so the **KL divergence is 0.9431**.
5. **Decode.** Suppose the decoder turns z into pixel probabilities y = (0.9, 0.2, 0.7).
6. **Reconstruction term**, log p(x | z) = Σ [x log y + (1 − x) log(1 − y)]:
   - pixel 1 (x = 1): log 0.9 = **−0.1054**.
   - pixel 2 (x = 0): log(1 − 0.2) = log 0.8 = **−0.2231**.
   - pixel 3 (x = 1): log 0.7 = **−0.3567**.
   - Total: **−0.6852**.
7. **The bound.** −0.9431 + (−0.6852) = **−1.6283**; the training loss is its negative,
   **1.6283**.
8. **Where the KL term is zero.** With μ = 0 and σ = 1 a dimension gives ½ × (1 + 0 − 0 − 1) =
   **0**: no penalty for matching the prior exactly.

**What this means:** the loss charges for a bad rebuild (0.6852) and for codes that stray from
the standard normal (0.9431). Dimension 2, pulled to −1 and narrowed to 0.5, cost far more than
dimension 1. Because z was built as μ + σ ⊙ ε, both costs can send gradients back to the encoder.
