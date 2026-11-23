---
id: architecture-era/gan
type: architecture-era
slug: gan
schema_version: 2.5
title: "Generative Adversarial Networks"
name: "Generative Adversarial Network"
era: foundation
era_range: "2014–2020"
year: 2014
family: generation
core_innovation: "Train a generator against a discriminator in a minimax game, learning to synthesize data with no explicit likelihood."
description: "Two neural networks trained against each other: a generator that turns random numbers into made-up samples, such as images, and a discriminator that tries to tell those samples from real ones."
key_features: ["Generator vs. discriminator minimax game", "Implicit (likelihood-free) generative model", "Sampling by a single forward pass", "Adversarial training dynamics"]
key_models: ["DCGAN (2015)", "BigGAN (2018)", "StyleGAN (2018)"]
paper_title: "Generative Adversarial Networks (Goodfellow et al., 2014)"
paper_url: "https://arxiv.org/abs/1406.2661"
influenced_by: []
led_to: [architecture-era/diffusion]

analogy:
  title: "Counterfeiters and the police"
  body: "This is an analogy, not the mechanism; the paper's authors use it themselves. A team of counterfeiters prints fake banknotes and tries to spend them, while the police examine notes and try to catch the fakes. Each side improves because of the other: caught fakes teach the counterfeiters what gave them away, and fakes that slip through teach the police what to look for. The contest ends, ideally, when the fakes cannot be told from genuine notes and the police can only guess. The counterfeiters are the generator, the police are the discriminator. The exact rule is the key equation below."

narrative:
  motivation: "**Generative models were held back by hard probability calculations.** The paper is by **Goodfellow, Pouget-Abadie, Mirza, Xu, Warde-Farley, Ozair, Courville and Bengio (2014)**, at the Université de Montréal.\n\n- **What worked then:** the authors write that the most striking successes of deep learning were **discriminative** models, which map an input such as an image to a class label.\n- **What did not:** deep **generative** models, which produce new data, had less impact because of the many **intractable probability computations** in maximum likelihood estimation.\n- **Boltzmann machines:** these needed a normalising sum over all states (the **partition function**) whose gradient could only be estimated by **Markov chain Monte Carlo (MCMC)** sampling, and poor mixing of the chains made learning hard."
  approach: "**Pit a generator against an adversary.**\n\n- **Generator G:** turns random noise z into a sample G(z).\n- **Discriminator D:** outputs one number, the **probability** that its input came from the training data rather than from G.\n- **Opposite goals:** D is trained to label real and generated samples correctly; G is trained to make D **wrong**.\n- **Only backpropagation:** with both players as **multilayer perceptrons**, the whole system trains with backpropagation and dropout, and sampling needs only a **forward pass**: no Markov chains and no approximate inference."
  solution: "**One value function, a minimax game, and a proof about its optimum.**\n\n- **The game:** D maximises and G minimises V(D, G), the key equation below.\n- **Training loop:** **k** steps of updating D, then one step of updating G; the authors used **k = 1**, the least expensive option, with momentum.\n- **Best discriminator:** for a fixed G, the optimal D at a point x is **p_data(x) / (p_data(x) + p_g(x))**.\n- **Global optimum:** with that D, the game's value equals **−log 4 + 2 · JSD(p_data ‖ p_g)**, where JSD is the Jensen–Shannon divergence; it is lowest **only when p_g = p_data**, and then D = **1/2** everywhere.\n- **A practical change:** early on, log(1 − D(G(z))) **saturates**, so the authors suggest training G to **maximise log D(G(z))** instead, which has the same fixed point and much stronger early gradients."
  outcome: "**A framework that later work scaled up.**\n\n- **First results:** on a Parzen-window estimate of test log-likelihood the authors report **225 ± 2** on MNIST digits (best in their table) and **2057 ± 26** on the Toronto Face Database (TFD), where a stacked contractive autoencoder scored **2110 ± 50**.\n- **DCGAN (2015):** **Radford, Metz and Chintala** give convolutional architecture guidelines that, they report, make GANs stable to train in most settings.\n- **WGAN (2017):** **Arjovsky, Chintala and Bottou** replace the objective and report no evidence of **mode collapse** in any of their experiments.\n- **Spectral normalization (2018):** **Miyato et al.** constrain the discriminator's layers to stabilise its training.\n- **BigGAN (2018):** **Brock, Donahue and Simonyan** raise the Inception Score on ImageNet at 128 × 128 from **52.52** to **166.5**.\n- **StyleGAN (2018):** **Karras, Laine and Aila** separate high-level attributes such as **pose and identity** from stochastic detail such as **freckles and hair**."

key_equation:
  latex: "\\min_{G}\\max_{D}\\; \\mathbb{E}_{x\\sim p_{\\text{data}}}[\\log D(x)] + \\mathbb{E}_{z\\sim p_{z}}[\\log(1 - D(G(z)))]"
  plain: "In words: V is the average of log D over real samples plus the average of log(1 − D) over generated samples. D raises V by scoring real samples near 1 and generated ones near 0. G lowers V by making D score its samples near 1. The worked example above computes V for a tiny made-up case and shows it reaching its lowest possible value, −log 4, when the two distributions match."
  terms:
    - { symbol: "G", meaning: "the generator: turns a noise vector z into a sample G(z); a multilayer perceptron in the paper" }
    - { symbol: "D", meaning: "the discriminator: outputs D(x), the probability that x came from the training data rather than from G" }
    - { symbol: "x ~ p_data", meaning: "a real sample drawn from the training data" }
    - { symbol: "z ~ p_z", meaning: "a noise vector drawn from a fixed, simple distribution; uniform in the paper's Figure 1 and in DCGAN" }
    - { symbol: "p_g", meaning: "the distribution of the generator's samples G(z); defined only by the generator, never written down as a formula" }
    - { symbol: "E", meaning: "the expected value: the average over many draws" }

strengths:
  - "**No Markov chains:** the authors list as advantages that Markov chains are **never needed**, only backpropagation is used to get gradients, and **no inference** is needed during learning."
  - "**Sampling in one pass:** a sample is one **forward pass** through the generator; the paper notes that its samples are uncorrelated because they do not depend on Markov chain mixing."
  - "**Sharp distributions:** the authors write that adversarial nets can represent **very sharp, even degenerate** distributions, while Markov-chain methods need the distribution to be somewhat blurry so the chains can mix."
  - "**Data reaches the generator only through D:** the generator is updated only with gradients flowing through the discriminator, so, the authors suggest, parts of the input are **not copied directly** into its parameters."
  - "**Scales up:** BigGAN's authors train with **2 to 4 times** as many parameters and **8 times** the batch size of earlier work and report an Inception Score of **166.5** against the previous **52.52**."
  - "**Controllable variants:** StyleGAN's authors report an unsupervised separation of **pose and identity** from stochastic detail, with **scale-specific** control of synthesis."

weaknesses:
  - "**No explicit likelihood:** there is no explicit formula for p_g(x); the paper estimates likelihood with Parzen windows, a method the authors say has **somewhat high variance** and does not work well in high dimensions."
  - "**D and G must stay in step:** the paper warns that G must not be trained too much without updating D, or G may map **too many values of z to the same x** (its \"Helvetica scenario\"), a failure later papers call **mode collapse**."
  - "**Weak gradients early on:** when G is poor, D rejects its samples confidently and **log(1 − D(G(z))) saturates**, which is why the paper suggests a different objective for G in practice."
  - "**Instability at scale:** BigGAN's authors report that their large models undergo **complete training collapse** and that full stability came only at a **dramatic cost** to performance."
  - "**A perfect discriminator stops learning:** Miyato et al. note that when the real and generated samples do not overlap, a discriminator can separate them perfectly, and the generator's training then **comes to a complete stop**."
  - "**Hard to evaluate:** BigGAN's authors call the Inception Score and Fréchet Inception Distance (FID) popular **despite notable flaws** and use them only as approximate measures."

comparison:
  before: "Boltzmann machines (deep undirected models)"
  after: "Adversarial nets"
  rows:
    - { aspect: "What training needs", before: "MCMC sampling to approximate the gradient of the partition function, plus inference", after: "Backpropagation only; the discriminator must be kept in step with the generator", source: "https://arxiv.org/abs/1406.2661" }
    - { aspect: "Drawing a sample", before: "Requires running a Markov chain", after: "One forward pass of noise through the generator", source: "https://arxiv.org/abs/1406.2661" }
    - { aspect: "Probability of a given data point, p(x)", before: "Intractable; may be approximated with annealed importance sampling (AIS)", after: "Not explicitly represented; may be approximated with Parzen density estimation", source: "https://arxiv.org/abs/1406.2661" }
    - { aspect: "Sharp or degenerate distributions", before: "Markov-chain methods need the distribution somewhat blurry so the chains can mix", after: "Can represent very sharp, even degenerate distributions", source: "https://arxiv.org/abs/1406.2661" }
    - { aspect: "How the model sees the data", before: "Updated directly with the training examples", after: "The generator never sees data; it learns from gradients that flow through the discriminator", source: "https://arxiv.org/abs/1406.2661" }

key_numbers:
  columns: ["Model", "Generator", "Discriminator and training", "Data and output", "Reported result"]
  rows:
    - { cells: ["GAN (2014)", "Multilayer perceptron with rectifier and sigmoid units; noise only at the bottom layer", "Multilayer perceptron with maxout units and dropout; k = 1 discriminator step per generator step, momentum", "MNIST, Toronto Face Database (TFD), CIFAR-10", "Parzen log-likelihood 225 ± 2 (MNIST), 2057 ± 26 (TFD)"], source: "https://arxiv.org/abs/1406.2661" }
    - { cells: ["DCGAN (2015)", "100 uniform noise numbers projected, then four fractionally-strided convolutions; ReLU, tanh output", "Strided convolutions, LeakyReLU (slope 0.2), one sigmoid output; Adam, learning rate 0.0002, β₁ = 0.5, mini-batch 128", "LSUN bedrooms (a little over 3 million images), ImageNet-1k, faces; 64 × 64 images from the LSUN generator", "82.8% CIFAR-10 accuracy from the features of a discriminator trained on ImageNet-1k, with a linear classifier"], source: "https://arxiv.org/abs/1511.06434" }
    - { cells: ["BigGAN (2018)", "Class-conditional, built on SA-GAN; 2 to 4 times the parameters of earlier work", "Hinge loss, 2 discriminator steps per generator step, batch size 2048; 128 to 512 TPU v3 cores", "ImageNet at 128 × 128, 256 × 256 and 512 × 512", "Inception Score 166.5 and FID 7.4 at 128 × 128 (previous best 52.52 and 18.65)"], source: "https://arxiv.org/abs/1809.11096" }
    - { cells: ["StyleGAN (2018)", "8-layer mapping network; 18-layer synthesis network (two layers per resolution, 4 × 4 to 1024 × 1024) starting from a learned constant; 26.2M trainable parameters", "Progressive GAN's networks and settings; on FFHQ the non-saturating loss with R1 regularization", "FFHQ: 70,000 face images at 1024 × 1024", "FID 4.40 on FFHQ (configuration F; lower is better)"], source: "https://arxiv.org/abs/1812.04948" }

misunderstandings:
  - misunderstanding: "The generator learns by copying real images."
    truth: "The generator **never sees real data**. It is updated only with gradients that flow back through the discriminator, and the authors suggest this means parts of the input are **not copied directly** into the generator's parameters. To show their model had not memorised the training set, they printed each sample next to its **nearest training example**."
    source: "https://arxiv.org/abs/1406.2661"
  - misunderstanding: "Training has gone well when the discriminator always spots the fakes."
    truth: "At the game's optimum the discriminator outputs **1/2 everywhere**: it cannot tell real from generated. A discriminator that wins outright is a problem; Miyato et al. note that when it separates the two perfectly, its gradient with respect to the input is **0** and the generator's training **comes to a complete stop**."
    source: "https://arxiv.org/abs/1802.05957"
  - misunderstanding: "GANs are trained with exactly the minimax objective as written."
    truth: "The original paper itself suggests training G to **maximise log D(G(z))** instead of minimising log(1 − D(G(z))), because the latter **saturates** early in training. Later models changed the objective further: BigGAN uses the **hinge loss** and takes **two** discriminator steps per generator step."
    source: "https://arxiv.org/abs/1809.11096"
  - misunderstanding: "The 2014 paper proved that GAN training always converges."
    truth: "The proof holds in the space of probability distributions, as if the networks had **unlimited capacity**, and assumes D reaches its optimum at every step. The authors write that with multilayer perceptrons the method has **multiple critical points** and lacks theoretical guarantees, and that it works well in practice despite this."
    source: "https://arxiv.org/abs/1406.2661"

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
  - { name: "GAN", year: 2014, note: "The original: a generator trained only through a discriminator's gradient, with no explicit likelihood; both players were multilayer perceptrons." }
  - { name: "DCGAN", year: 2015, note: "Deep convolutional GANs with architectural guidelines (strided convolutions, batch normalization, no fully connected hidden layers); the authors report a hierarchy of representations from object parts to scenes." }
  - { name: "BigGAN", year: 2018, note: "Class-conditional GANs trained at the largest scale then attempted; Inception Score 166.5 and FID 7.4 on ImageNet at 128×128, against a previous best of 52.52 and 18.65." }
  - { name: "StyleGAN", year: 2018, note: "A style-based generator that separates high-level attributes (pose, identity on faces) from stochastic variation (freckles, hair), with scale-specific control; introduced the FFHQ face dataset." }

resources:
  - { kind: paper, title: "Generative Adversarial Networks (Goodfellow et al., 2014)", url: "https://arxiv.org/abs/1406.2661" }
  - { kind: blog, title: "From GAN to WGAN (Lilian Weng)", url: "https://lilianweng.github.io/posts/2017-08-20-gan/" }
  - { kind: tutorial, title: "DCGAN Tutorial (PyTorch)", url: "https://pytorch.org/tutorials/beginner/dcgan_faces_tutorial.html" }
  - { kind: video, title: "Generative Adversarial Networks explained (StatQuest)", url: "https://www.youtube.com/watch?v=8L11aMN5KY8" }

learn_first:
  - { label: "Neural networks & backpropagation", href: "/learn/concepts/backpropagation", tag: "Math" }
  - { label: "What a generative model is", href: "/learn/concepts/generative-models", tag: "Concept" }
  - { label: "Convolutional networks (the generator/discriminator backbone)", href: "/models/architecture/cnn", tag: "Architecture" }

tags: [gan, generation, adversarial, image-synthesis]
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/1406.2661", title: "Generative Adversarial Networks (Goodfellow et al., arXiv:1406.2661, 2014; the paper's PDF is titled Generative Adversarial Nets)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1511.06434", title: "Unsupervised Representation Learning with Deep Convolutional Generative Adversarial Networks (Radford, Metz & Chintala, arXiv:1511.06434, 2015)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1701.07875", title: "Wasserstein GAN (Arjovsky, Chintala & Bottou, arXiv:1701.07875, 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1802.05957", title: "Spectral Normalization for Generative Adversarial Networks (Miyato et al., arXiv:1802.05957, 2018)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1809.11096", title: "Large Scale GAN Training for High Fidelity Natural Image Synthesis (Brock, Donahue & Simonyan, arXiv:1809.11096, 2018)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1812.04948", title: "A Style-Based Generator Architecture for Generative Adversarial Networks (Karras, Laine & Aila, arXiv:1812.04948, 2018)", trust: 0.95 }
---

## What Changed

Many deep generative models before GANs, such as **Boltzmann machines**, defined a probability
for every possible data point. Training and sampling them needed chains of random steps. A GAN
writes down no probability at all: it learns a **generator** that turns noise into samples, judged
by a second network.

How to read the table above (it restates Table 2 and Section 6 of Goodfellow et al., 2014):

- **Partition function:** the sum over every possible state that turns a Boltzmann machine's
  scores into probabilities; it is intractable for all but trivial models.
- **Markov chain Monte Carlo (MCMC):** drawing samples by taking many small random steps; the chain
  must **mix**, that is, wander between all the likely regions, before its samples can be trusted.
- **p(x):** the probability the model gives to one particular data point. A GAN has no formula for
  it, so the paper can only estimate it by placing a small Gaussian bump on each generated sample
  (a **Parzen window** estimate).
- **Sharp distribution:** one whose probability is concentrated on a few points or a thin region
  instead of being spread out smoothly.

## Key Ideas

- **Two players, one score:** the discriminator raises the value V, the generator lowers it.
- **No likelihood:** the generator's distribution is defined only by its samples.
- **Learning through the judge:** the generator's only training signal is the discriminator's
  gradient.
- **The ideal end point:** the generator's samples match the data and the discriminator can only
  answer 1/2.

## How It Works

The figure above shows one training round. Noise enters on the left; the two loops show which
network each update trains.

A **sample** is one data item, such as one image. A **distribution** says how likely each possible
sample is. A **gradient** tells each weight which way to move to raise or lower a score.

1. **Noise z (left box).** A vector of random numbers is drawn from a fixed, simple distribution
   p_z. DCGAN uses **100** numbers drawn uniformly; the 2014 experiments feed noise only into the
   generator's bottom layer.
2. **Generator G.** A network turns z into a sample **G(z)**, the "fake". In the 2014 paper it is a
   multilayer perceptron with rectifier and sigmoid units. Many draws of z give many samples, and
   their spread is the generator's distribution **p_g**.
3. **Real sample x (lower box).** A sample taken from the training data, whose distribution is
   **p_data**.
4. **Discriminator D.** A second network reads one sample and outputs one number **D(·)** between
   0 and 1: its probability that the sample is real. In the 2014 paper D is a multilayer perceptron
   with maxout units and dropout; DCGAN ends it with a single sigmoid output.
5. **Train D (lower loop).** Take a mini-batch of **m** noise vectors and **m** real samples, and
   move D's weights to **raise** the average of log D(x) + log(1 − D(G(z))): D(x) toward 1 on real
   samples, D(G(z)) toward 0 on fakes. The paper repeats this **k** times; its experiments use
   **k = 1**.
6. **Train G (upper loop).** Take a fresh mini-batch of noise and move G's weights to **lower** the
   average of log(1 − D(G(z))), that is, to push D(G(z)) toward 1. The gradient passes **back
   through D** into G; D's weights stay fixed in this step, and G never sees a real sample. In
   practice the paper suggests raising log D(G(z)) instead (the worked example shows why).
7. **Repeat.** The two steps alternate with any standard gradient method; the authors used
   momentum. The ideal end point, printed under the figure, is **p_g = p_data** with **D = 1/2**
   everywhere.

## Worked Example

**The value of the game for a tiny made-up dataset**, then why the generator's loss is changed in
practice. The numbers are made up so every step can be checked by hand: an example, not from a
trained model. Logarithms are natural logarithms (ln).

Suppose every possible "image" is one of three items, A, B and C, with these probabilities:

| Item | Real data, p_data | Generator, p_g |
| --- | --- | --- |
| A | 0.5 | 0.25 |
| B | 0.25 | 0.25 |
| C | 0.25 | 0.5 |

1. **The best discriminator.** For a fixed generator the paper's Proposition 1 gives
   D*(x) = p_data(x) / (p_data(x) + p_g(x)):
   - A: 0.5 / (0.5 + 0.25) = 0.5 / 0.75 ≈ **0.667**
   - B: 0.25 / (0.25 + 0.25) = **0.5**
   - C: 0.25 / (0.25 + 0.5) = 0.25 / 0.75 ≈ **0.333**
   - D* is most confident "real" where real data is more common than fakes, and unsure where both
     are equally common.
2. **The real-data term** of the key equation, the average of ln D*(x) over real data:
   - 0.5 × ln 0.667 = 0.5 × (−0.405) ≈ −0.203
   - 0.25 × ln 0.5 = 0.25 × (−0.693) ≈ −0.173
   - 0.25 × ln 0.333 = 0.25 × (−1.099) ≈ −0.275
   - Sum: **−0.651**.
3. **The generated-data term**, the average of ln(1 − D*(x)) over the generator's samples:
   - 0.25 × ln(1 − 0.667) = 0.25 × (−1.099) ≈ −0.275
   - 0.25 × ln(1 − 0.5) = 0.25 × (−0.693) ≈ −0.173
   - 0.5 × ln(1 − 0.333) = 0.5 × (−0.405) ≈ −0.203
   - Sum: **−0.651**.
4. **The value of the game.** V = −0.651 + (−0.651) ≈ **−1.301**. The lowest possible value is
   −ln 4 ≈ **−1.386**. The gap, 1.386 − 1.301 ≈ **0.085**, is twice the Jensen–Shannon divergence
   between the two distributions (so that divergence is about 0.042): it measures how far the
   generator still is from the data.
5. **The generator improves until p_g = p_data** = (0.5, 0.25, 0.25):
   - D* becomes 0.5 / (0.5 + 0.5) = **0.5** for every item: the discriminator can only guess.
   - V = 1 × ln 0.5 + 1 × ln 0.5 = −0.693 − 0.693 ≈ **−1.386** = −ln 4, the paper's Theorem 1.
6. **Why the generator's loss is changed in practice.** Early on, D rejects a poor fake
   confidently, say **D(G(z)) = 0.01**. How fast each loss changes as D(G(z)) moves:
   - The minimax loss ln(1 − D) has slope −1 / (1 − 0.01) ≈ **−1.01**: a weak signal.
   - The suggested objective ln D has slope 1 / 0.01 = **100**: about **99 times** stronger.
   - Both push D(G(z)) the same way, upward; the authors state that the change keeps the same
     fixed point of the training dynamics.

**What this means:** the discriminator's best answer is a ratio of how common a sample is in the
data and in the fakes, and the game's value tells the generator how far apart the two
distributions are. Only when they match does the value reach −ln 4 and the discriminator fall to
1/2. Early in training the paper's own loss gives the generator almost no signal, so in practice
it is trained to raise ln D(G(z)).
