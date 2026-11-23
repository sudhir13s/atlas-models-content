---
id: architecture-era/mlp-backprop
type: architecture-era
slug: mlp-backprop
schema_version: 2.7
title: "Multilayer Perceptron & Backpropagation"
name: "Multilayer Perceptron (Backpropagation)"
era: early
era_range: "1986–2011"
year: 1986
family: classical-nn
core_innovation: "Hidden layers of smooth units whose weights are all learned: the error at the output is passed backwards through the network to give the gradient of the error for every weight."
description: "A neural network with one or more hidden layers between input and output, in which every weight, the hidden ones included, is learned by sending the output error backwards through the network to work out how each weight should change."
key_features: ["Hidden layers of units with a smooth, bounded-slope activation (the logistic function in 1986)", "Forward pass, then a backward pass of derivatives through the same connections", "Gradient descent on the total squared error", "Hidden units learn their own features", "Universal approximation with one hidden layer (Cybenko, 1989)"]
key_models: ["Rumelhart, Hinton & Williams networks (1986)", "NETtalk (1987)", "LeCun et al. zip-code network (1989)"]
paper_title: "Learning Representations by Back-Propagating Errors"
paper_url: "https://doi.org/10.1038/323533a0"
influenced_by: [architecture-era/perceptron]
led_to: [architecture-era/cnn, architecture-era/rnn-lstm]

analogy:
  title: "A kitchen brigade passing blame back down the line"
  body: "This is an analogy, not the mechanism. In a restaurant kitchen, prep cooks chop, line cooks assemble and the head chef plates, and only the final plate is judged. When a dish comes back too salty, the complaint travels backwards: the head chef works out how much of the saltiness came from the line cook's step, the line cook does the same for the prep cook, and each adjusts their own step by their share. Backpropagation passes the output error back the same way, layer by layer; the exact rule is the key equation below."

narrative:
  motivation: "**Hidden units have no target to learn from.** The problem is set out by **Rumelhart, Hinton and Williams (1986)**.\n\n- **The easy case:** when inputs connect **directly** to outputs, the authors write, it is relatively easy to find rules that steadily reduce the gap between actual and desired outputs.\n- **The hard case:** learning is more difficult with **hidden units**, whose actual or desired states the task does not specify.\n- **What perceptrons had:** units between input and output whose input connections are **fixed**, so, in the authors' words, they do not learn representations.\n- **A task that needs them:** detecting whether a row of binary inputs is **mirror-symmetric** is impossible by connecting inputs straight to outputs, because one input alone says nothing about symmetry."
  approach: "**Make every unit smooth, then descend the error.**\n\n- **Smooth units:** each unit outputs a real number, the **logistic function** of its total input, instead of a hard yes or no; any function with a bounded derivative would do, the authors write.\n- **One error number:** the total error **E** is half the sum of squared differences between actual and desired outputs, over all cases and output units.\n- **Follow the slope:** to lower E by **gradient descent**, the procedure needs the derivative of E with respect to **every weight**.\n- **An earlier attempt:** **Rosenblatt's 1961** report already describes a **back-propagating error correction procedure** for perceptrons with a learned layer before the response units, but it assigns errors to hidden units by a **probabilistic** rule, not by derivatives."
  solution: "**Back-propagation: a forward pass, then a backward pass of derivatives.**\n\n- **Forward:** layer by layer from the bottom, each unit sums its weighted inputs and applies the logistic function.\n- **Backward:** starting from **∂E/∂y = y − d** at the outputs, the **chain rule** gives the derivative for each unit's total input, then for each weight, then for each unit in the layer below.\n- **Update:** each weight changes by **−ε ∂E/∂w**; the authors speed this up with a **momentum** term α that carries over part of the previous change.\n- **Start:** small random weights, to **break symmetry** between hidden units.\n- **Independent finds:** the paper notes that variants were discovered independently by **David Parker** and **Yann Le Cun**."
  outcome: "**Hidden units that learn features, and the training loop later networks use.**\n\n- **Symmetry solved:** the procedure found a solution with just **two hidden units**, after **1,425 sweeps** through the 64 possible inputs.\n- **Family trees:** a **five-layer** network trained on **100 of 104** relationship facts answered the **4 unseen** ones correctly, with hidden units encoding features such as nationality and generation.\n- **NETtalk (1987):** **Sejnowski and Rosenberg** trained a network of **203** input, **80** hidden and **26** output units to pronounce English text.\n- **Zip codes (1989):** **LeCun et al.** applied back-propagation to handwritten zip code digits from the US Mail, feeding the network **images** rather than feature vectors.\n- **Universal approximation (1989):** **Cybenko** proved that one hidden layer with any continuous sigmoidal function can approximate any continuous function on the unit cube as closely as wanted."

key_equation:
  latex: "\\dfrac{\\partial E}{\\partial w_{ji}} = \\underbrace{\\dfrac{\\partial E}{\\partial y_j}\\, y_j\\,(1 - y_j)}_{\\partial E / \\partial x_j}\\; y_i, \\qquad \\dfrac{\\partial E}{\\partial y_i} = \\sum_j \\dfrac{\\partial E}{\\partial x_j}\\, w_{ji}, \\qquad \\Delta w_{ji} = -\\varepsilon\\, \\dfrac{\\partial E}{\\partial w_{ji}}"
  plain: "In words: for a weight w_ji from unit i to unit j, take how the error changes with unit j's output, multiply by the slope of the logistic function at that unit, y_j(1 − y_j), to get how the error changes with unit j's total input, then multiply by the output y_i that travels along the weight. To go one layer down, add up those total-input derivatives over every unit j that unit i feeds, each times the weight between them. Finally move every weight a step ε against its derivative. At the output units the starting value is ∂E/∂y_j = y_j − d_j. The worked example above runs each formula once by hand."
  terms:
    - { symbol: "E", meaning: "the total error: half the sum, over cases and output units, of (actual output − desired output)²" }
    - { symbol: "x_j", meaning: "the total input to unit j: the sum of y_i × w_ji over the units i that feed it" }
    - { symbol: "y_j", meaning: "the output of unit j: the logistic function 1 / (1 + e^(−x_j)) of its total input" }
    - { symbol: "d_j", meaning: "the desired output of output unit j for this case" }
    - { symbol: "w_ji", meaning: "the weight on the connection from unit i to unit j" }
    - { symbol: "ε", meaning: "the step size of gradient descent; 0.1 for the paper's symmetry network" }

strengths:
  - "**Hidden units learn features:** the authors report that hidden units come to represent **important features of the task domain**; in the family-tree network one unit separated English from Italian people."
  - "**All derivatives in one backward pass:** the backward pass reuses the forward pass's values and, the authors note, can be implemented by **local computations** in parallel hardware."
  - "**Generalisation:** the family-tree network answered the **4 facts** it was not trained on correctly; NETtalk, trained on **1,000** common words, scored **77%** on the whole **20,012-word** dictionary before any training on it."
  - "**One hidden layer is enough in principle:** Cybenko proved that a single hidden layer with any continuous sigmoidal function can **uniformly approximate** any continuous function on the unit cube."
  - "**Also for recurrent networks:** the authors show that a recurrent network run for several steps is equivalent to a **layered network** with shared weights, so the same procedure applies."

weaknesses:
  - "**Local minima:** gradient descent is **not guaranteed** to find a global minimum. The authors report that networks rarely get stuck in poor local minima, and that they saw it only in networks with **just enough connections** for the task."
  - "**Slow convergence:** plain gradient descent converges less rapidly than methods using **second derivatives**, the authors write; the 6-input symmetry task took **1,425 sweeps**."
  - "**Saturating units in deep networks:** **Glorot and Bengio (2010)** find the logistic sigmoid **unsuited for deep networks** with random initialisation, because its mean value can drive the top hidden layer into **saturation**."
  - "**Existence, not size:** Cybenko's theorem says nothing about how many hidden units are needed; he suspects the overwhelming majority of problems would need **astronomical numbers** of them."
  - "**Not a model of the brain:** the authors state that the procedure, in its current form, is **not a plausible model** of learning in brains."

visual:
  kind: backprop
  inputs: [1, 0]
  target: 1
  hidden_weights: [[0.5, -0.5], [-1, 1]]
  output_weights: [1, 1]
  rate: 1
  caption: "The worked example's 2-2-1 network, computed: each unit's weighted sum x and output y from the forward pass, and ∂E/∂x from the backward pass; the table gives each weight's derivative and its value after one step with ε = 1. The weights and the example are made up: an example, not from a trained model."

comparison:
  before: "Perceptron (error correction)"
  after: "Multilayer network (back-propagation)"
  rows:
    - { aspect: "Which connections learn", before: "Only those into the output unit; the association units' inputs stay fixed", after: "All of them, including the connections into hidden units", source: "https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf" }
    - { aspect: "How weight changes are found", before: "With inputs wired straight to outputs, relatively easy rules reduce the gap between actual and desired outputs", after: "The derivative of the total error for every weight, from a backward pass; then Δw = −ε ∂E/∂w", source: "https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf" }
    - { aspect: "Mirror-symmetry detection", before: "Impossible by connecting inputs straight to outputs", after: "Solved with two hidden units after 1,425 sweeps", source: "https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf" }
    - { aspect: "Pronouncing English (NETtalk, 1,000-word corpus)", before: "No hidden units: performance saturated at 82%", after: "120 hidden units: 98%", source: "https://content.wolfram.com/sites/13/2018/02/01-1-10.pdf" }
key_numbers:
  columns: ["Network", "Units in each layer", "Training", "Reported result"]
  rows:
    - { cells: ["Symmetry detector (Rumelhart, Hinton & Williams, 1986)", "6 input, 2 hidden, 1 output", "1,425 sweeps through all 64 inputs; ε = 0.1, α = 0.9; starting weights uniform between −0.3 and 0.3", "Detects mirror symmetry; weights on each side of the midpoint in the ratio 1:2:4"], source: "https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf" }
    - { cells: ["Family trees (Rumelhart, Hinton & Williams, 1986)", "Five layers: 24 + 12 input, 6 + 6, 12, 6, then one output unit per person", "100 of the 104 possible facts; 1,500 sweeps; ε = 0.005 and α = 0.5 for the first 20 sweeps, then ε = 0.01 and α = 0.9", "Correct on the 4 facts it was not trained on"], source: "https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf" }
    - { cells: ["NETtalk (Sejnowski & Rosenberg, 1987)", "203 input (a 7-letter window), 80 hidden, 26 output; 18,629 weights", "The 1,000 most common words of a 20,012-word dictionary; starting weights uniform between −0.3 and 0.3", "With 120 hidden units: 98% on the 1,000 words"], source: "https://content.wolfram.com/sites/13/2018/02/01-1-10.pdf" }

misunderstandings:
  - misunderstanding: "Backpropagation is the whole learning algorithm."
    truth: "In the 1986 paper, back-propagation is the **backward pass** that computes ∂E/∂w for every weight. The weights are then changed by **gradient descent**, Δw = −ε ∂E/∂w, which the authors accelerate with a **momentum** term; they accumulated ∂E/∂w over **all cases** before each change."
    source: "https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf"
  - misunderstanding: "Rumelhart, Hinton and Williams were the only ones to find back-propagation."
    truth: "Their paper states that variants of the procedure were discovered **independently by David Parker** (personal communication) **and by Yann Le Cun**, citing Le Cun's 1985 paper in *Proceedings of Cognitiva*."
    source: "https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf"
  - misunderstanding: "Gradient descent with back-propagation always finds the best weights."
    truth: "The authors call **local minima** the most obvious drawback: gradient descent is **not guaranteed** to reach a global minimum. In their experience it rarely got stuck in poor minima, and adding a few more connections gave **paths around** the barriers."
    source: "https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf"
  - misunderstanding: "The universal approximation theorem means one hidden layer is all you need in practice."
    truth: "Cybenko writes that his results concern **existence** only. How many hidden units are needed is left open, and he suspects that the overwhelming majority of approximation problems would require **astronomical numbers** of them."
    source: "https://papers.baulab.info/papers/Cybenko-1989.pdf"

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 420" role="img" aria-label="A multilayer perceptron: four inputs, two hidden layers of five neurons and three outputs, every neuron connected to every neuron of the next layer; the forward pass runs left to right to a loss, and backpropagation carries the loss gradient right to left.">
  <defs><marker id="mlp-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="mlp-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <path class="d-line-strong" d="M90 38 H760" marker-end="url(#mlp-arrow)"/>
  <text class="d-label" x="425" y="28" text-anchor="middle">Forward pass: each layer computes a weighted sum, then a non-linearity σ</text>
  <path class="d-line" d="M103 136 L267 113"/>
  <path class="d-line" d="M103 136 L267 159"/>
  <path class="d-line" d="M103 136 L267 205"/>
  <path class="d-line" d="M103 136 L267 251"/>
  <path class="d-line" d="M103 136 L267 297"/>
  <path class="d-line" d="M103 182 L267 113"/>
  <path class="d-line" d="M103 182 L267 159"/>
  <path class="d-line" d="M103 182 L267 205"/>
  <path class="d-line" d="M103 182 L267 251"/>
  <path class="d-line" d="M103 182 L267 297"/>
  <path class="d-line" d="M103 228 L267 113"/>
  <path class="d-line" d="M103 228 L267 159"/>
  <path class="d-line" d="M103 228 L267 205"/>
  <path class="d-line" d="M103 228 L267 251"/>
  <path class="d-line" d="M103 228 L267 297"/>
  <path class="d-line" d="M103 274 L267 113"/>
  <path class="d-line" d="M103 274 L267 159"/>
  <path class="d-line" d="M103 274 L267 205"/>
  <path class="d-line" d="M103 274 L267 251"/>
  <path class="d-line" d="M103 274 L267 297"/>
  <text class="d-math" x="185" y="86" text-anchor="middle">W¹</text>
  <path class="d-line" d="M293 113 L457 113"/>
  <path class="d-line" d="M293 113 L457 159"/>
  <path class="d-line" d="M293 113 L457 205"/>
  <path class="d-line" d="M293 113 L457 251"/>
  <path class="d-line" d="M293 113 L457 297"/>
  <path class="d-line" d="M293 159 L457 113"/>
  <path class="d-line" d="M293 159 L457 159"/>
  <path class="d-line" d="M293 159 L457 205"/>
  <path class="d-line" d="M293 159 L457 251"/>
  <path class="d-line" d="M293 159 L457 297"/>
  <path class="d-line" d="M293 205 L457 113"/>
  <path class="d-line" d="M293 205 L457 159"/>
  <path class="d-line" d="M293 205 L457 205"/>
  <path class="d-line" d="M293 205 L457 251"/>
  <path class="d-line" d="M293 205 L457 297"/>
  <path class="d-line" d="M293 251 L457 113"/>
  <path class="d-line" d="M293 251 L457 159"/>
  <path class="d-line" d="M293 251 L457 205"/>
  <path class="d-line" d="M293 251 L457 251"/>
  <path class="d-line" d="M293 251 L457 297"/>
  <path class="d-line" d="M293 297 L457 113"/>
  <path class="d-line" d="M293 297 L457 159"/>
  <path class="d-line" d="M293 297 L457 205"/>
  <path class="d-line" d="M293 297 L457 251"/>
  <path class="d-line" d="M293 297 L457 297"/>
  <text class="d-math" x="375" y="86" text-anchor="middle">W²</text>
  <path class="d-line" d="M483 113 L647 159"/>
  <path class="d-line" d="M483 113 L647 205"/>
  <path class="d-line" d="M483 113 L647 251"/>
  <path class="d-line" d="M483 159 L647 159"/>
  <path class="d-line" d="M483 159 L647 205"/>
  <path class="d-line" d="M483 159 L647 251"/>
  <path class="d-line" d="M483 205 L647 159"/>
  <path class="d-line" d="M483 205 L647 205"/>
  <path class="d-line" d="M483 205 L647 251"/>
  <path class="d-line" d="M483 251 L647 159"/>
  <path class="d-line" d="M483 251 L647 205"/>
  <path class="d-line" d="M483 251 L647 251"/>
  <path class="d-line" d="M483 297 L647 159"/>
  <path class="d-line" d="M483 297 L647 205"/>
  <path class="d-line" d="M483 297 L647 251"/>
  <text class="d-math" x="565" y="86" text-anchor="middle">W³</text>
  <circle class="d-node d-role-input" cx="90" cy="136" r="13"/>
  <text class="d-note" x="90" y="139.5" text-anchor="middle">x₁</text>
  <circle class="d-node d-role-input" cx="90" cy="182" r="13"/>
  <text class="d-note" x="90" y="185.5" text-anchor="middle">x₂</text>
  <circle class="d-node d-role-input" cx="90" cy="228" r="13"/>
  <text class="d-note" x="90" y="231.5" text-anchor="middle">x₃</text>
  <circle class="d-node d-role-input" cx="90" cy="274" r="13"/>
  <text class="d-note" x="90" y="277.5" text-anchor="middle">x₄</text>
  <text class="d-title" x="90" y="346" text-anchor="middle">Input layer</text>
  <text class="d-note" x="90" y="362" text-anchor="middle">features x</text>
  <circle class="d-node d-role-feedforward" cx="280" cy="113" r="13"/>
  <circle class="d-node d-role-feedforward" cx="280" cy="159" r="13"/>
  <circle class="d-node d-role-feedforward" cx="280" cy="205" r="13"/>
  <circle class="d-node d-role-feedforward" cx="280" cy="251" r="13"/>
  <circle class="d-node d-role-feedforward" cx="280" cy="297" r="13"/>
  <text class="d-title" x="280" y="346" text-anchor="middle">Hidden layer 1</text>
  <text class="d-note" x="280" y="362" text-anchor="middle">h¹ = σ(W¹x + b¹)</text>
  <circle class="d-node d-role-feedforward" cx="470" cy="113" r="13"/>
  <circle class="d-node d-role-feedforward" cx="470" cy="159" r="13"/>
  <circle class="d-node d-role-feedforward" cx="470" cy="205" r="13"/>
  <circle class="d-node d-role-feedforward" cx="470" cy="251" r="13"/>
  <circle class="d-node d-role-feedforward" cx="470" cy="297" r="13"/>
  <text class="d-title" x="470" y="346" text-anchor="middle">Hidden layer 2</text>
  <text class="d-note" x="470" y="362" text-anchor="middle">h² = σ(W²h¹ + b²)</text>
  <circle class="d-node d-role-output" cx="660" cy="159" r="13"/>
  <text class="d-note" x="660" y="162.5" text-anchor="middle">ŷ₁</text>
  <circle class="d-node d-role-output" cx="660" cy="205" r="13"/>
  <text class="d-note" x="660" y="208.5" text-anchor="middle">ŷ₂</text>
  <circle class="d-node d-role-output" cx="660" cy="251" r="13"/>
  <text class="d-note" x="660" y="254.5" text-anchor="middle">ŷ₃</text>
  <text class="d-title" x="660" y="346" text-anchor="middle">Output layer</text>
  <text class="d-note" x="660" y="362" text-anchor="middle">ŷ = σ(W³h² + b³)</text>
  <path class="d-line-strong" d="M676 159 L708 205 M676 251 L708 205"/>
  <path class="d-line-strong" d="M676 205 H742" marker-end="url(#mlp-arrow)"/>
  <rect class="d-box d-role-attention" x="746" y="179" width="98" height="52" rx="5"/>
  <text class="d-title" x="795" y="201" text-anchor="middle">Loss L(ŷ, y)</text>
  <text class="d-note" x="795" y="217" text-anchor="middle">error vs true label y</text>
  <path class="d-line-accent" stroke-dasharray="5 4" d="M795 231 V392 H90" marker-end="url(#mlp-arrow-accent)"/>
  <text class="d-label" x="425" y="384" text-anchor="middle">Backward pass: the chain rule carries ∂L/∂W back layer by layer; every weight moves against its gradient</text>
  </svg>
diagram_caption: "A multilayer perceptron with two hidden layers: every neuron feeds every neuron of the next layer. The forward pass computes a prediction and its loss; backpropagation carries the loss gradient back to update every weight."
notable_models:
  - { name: "Rumelhart–Hinton–Williams networks", year: 1986, note: "A 6-2-1 network that learned to detect mirror symmetry and a five-layer network that learned two family trees, both trained by back-propagation." }
  - { name: "NETtalk", year: 1987, note: "Sejnowski and Rosenberg's network of 203 input, 80 hidden and 26 output units (18,629 weights) that learned to pronounce English text from a 7-letter window." }
  - { name: "LeCun et al. zip-code network", year: 1989, note: "Back-propagation applied to handwritten zip code digits from the US Mail, with the network fed images rather than feature vectors and constraints from the task built into its architecture." }

resources:
  - { kind: paper, title: "Learning Representations by Back-Propagating Errors (Rumelhart, Hinton & Williams, 1986)", url: "https://doi.org/10.1038/323533a0" }
  - { kind: video, title: "Backpropagation, intuitively (3Blue1Brown)", url: "https://www.youtube.com/watch?v=Ilg3gGewQ5U" }
  - { kind: video, title: "The spelled-out intro to backpropagation (Andrej Karpathy, micrograd)", url: "https://www.youtube.com/watch?v=VMj-3S1tku0" }
  - { kind: tutorial, title: "Calculus on Computational Graphs: Backpropagation (Chris Olah)", url: "https://colah.github.io/posts/2015-08-Backprop/" }

learn_first:
  - { label: "The perceptron", href: "/models/architecture/perceptron", tag: "Architecture" }
  - { label: "The chain rule", href: "/learn/concepts/chain-rule", tag: "Math" }
  - { label: "Gradient descent", href: "/learn/concepts/gradient-descent", tag: "Concept" }
  - { label: "Activation functions", href: "/learn/concepts/activation-functions", tag: "Concept" }

tags: [mlp, backpropagation, classical-nn, foundational]
updated: '2026-11-23'
sources:
  - { url: "https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf", title: "Learning Representations by Back-Propagating Errors (Rumelhart, Hinton & Williams, Nature 323, 533-536, 1986; doi:10.1038/323533a0)", trust: 0.95 }
  - { url: "https://papers.baulab.info/papers/also/Rosenblatt-1961.pdf", title: "Principles of Neurodynamics: Perceptrons and the Theory of Brain Mechanisms (Rosenblatt, Cornell Aeronautical Laboratory Report VG-1196-G-8, 15 March 1961; DTIC AD 256582)", trust: 0.9 }
  - { url: "https://papers.baulab.info/papers/Cybenko-1989.pdf", title: "Approximation by Superpositions of a Sigmoidal Function (Cybenko, Mathematics of Control, Signals, and Systems 2, 303-314, 1989; doi:10.1007/BF02551274)", trust: 0.95 }
  - { url: "https://proceedings.mlr.press/v9/glorot10a.html", title: "Understanding the Difficulty of Training Deep Feedforward Neural Networks (Glorot & Bengio, AISTATS 2010)", trust: 0.95 }
  - { url: "https://content.wolfram.com/sites/13/2018/02/01-1-10.pdf", title: "Parallel Networks that Learn to Pronounce English Text (Sejnowski & Rosenberg, Complex Systems 1, 145-168, 1987)", trust: 0.95 }
  - { url: "http://yann.lecun.com/exdb/publis/pdf/lecun-89e.pdf", title: "Backpropagation Applied to Handwritten Zip Code Recognition (LeCun et al., Neural Computation 1, 541-551, 1989; doi:10.1162/neco.1989.1.4.541)", trust: 0.95 }
---

## What Changed

A perceptron learns only the connections into its output unit; the units before it keep
whatever wiring they were given. A multilayer network trained by **back-propagation** learns
**every** connection, so its hidden units build their own features.

How to read the table above:

- **Hidden unit:** a unit that is neither an input nor an output; the task says nothing about
  what it should do.
- **Logistic function:** 1 / (1 + e^(−x)), a smooth S-shaped curve from 0 to 1. Its smoothness
  is what makes the derivatives of the error exist.
- **Mirror symmetry:** whether a row of 0s and 1s reads the same from both ends. One input on
  its own says nothing about it, so a network needs hidden units to detect it.
- **NETtalk's percentages** are the share of correct phonemes (speech sounds) the network
  chose, on the 1,000 training words.

## Key Ideas

- **Hidden layers:** units between input and output that learn to represent features of the
  task.
- **Smooth units:** each unit outputs the logistic function of its summed input, so the error
  changes smoothly as any weight changes.
- **Backward pass:** the chain rule turns the error at the output into a derivative for every
  weight, one layer at a time, from the top down.
- **Gradient descent:** every weight moves a small step in the direction that lowers the error.

## How It Works

The figure above has **four inputs**, two hidden layers of **five units** each and **three
outputs**; every unit feeds every unit of the next layer. Follow the solid arrow to the right,
then the dashed arrow back.

A **weight** is a number on one connection; W¹, W² and W³ are the three layers of weights. The
figure's loss L is the total error E of Rumelhart, Hinton and Williams.

1. **Input layer x.** The example's numbers are placed on the input units.
2. **Hidden layer 1.** Each unit adds up its inputs, each times its weight, plus a bias, and
   applies σ, the logistic function: h¹ = σ(W¹x + b¹). The paper treats a bias as the weight
   of an extra input that is always 1.
3. **Hidden layer 2.** The same with the outputs of layer 1 as inputs: h² = σ(W²h¹ + b²). The
   paper allows any number of such layers; connections go upwards only, and may skip layers.
4. **Output layer.** Again a weighted sum and σ, giving the outputs ŷ₁ to ŷ₃.
5. **Loss.** The outputs are compared with the desired ones: E = ½ Σ (y − d)², summed over
   output units and over all training cases.
6. **Backward pass (dashed arrow), at the outputs.** For each output unit, ∂E/∂y = y − d. Times
   the logistic's slope y(1 − y) this gives ∂E/∂x, how the error changes with the unit's total
   input.
7. **Backward pass, through each layer of weights.** A weight's derivative is ∂E/∂x of the unit
   it feeds times the output of the unit it comes from. For each unit below, ∂E/∂y is the sum of
   ∂E/∂x over the units it feeds, each times the connecting weight. Repeat down to W¹.
8. **Update.** Every weight changes by −ε ∂E/∂w. The 1986 paper adds momentum: Δw(t) =
   −ε ∂E/∂w(t) + α Δw(t − 1), with t counting sweeps through all the training cases.
9. **Repeat.** Sweep through the training cases again and again until the error is small. The
   symmetry network of the paper needed **1,425** sweeps.

## Worked Example

**One forward and one backward pass by hand**, in a smaller network than the figure: 2 inputs,
2 hidden units, 1 output, no biases. The weights and the example are made up so every step can
be checked: an example, not from a trained model.

The input is y₁ = 1, y₂ = 0 and the desired output is d = 1. Units 3 and 4 are hidden, unit 5 is
the output. Numbers are rounded to 4 decimals; each step uses the unrounded values.

| Weight | w₃₁ | w₃₂ | w₄₁ | w₄₂ | w₅₃ | w₅₄ |
| --- | --- | --- | --- | --- | --- | --- |
| Value | 0.5 | −0.5 | −1 | 1 | 1 | 1 |

1. **Hidden units, forward.**
   - x₃ = 0.5 × 1 + (−0.5) × 0 = **0.5**, so y₃ = 1 / (1 + e^(−0.5)) = **0.6225**.
   - x₄ = (−1) × 1 + 1 × 0 = **−1**, so y₄ = 1 / (1 + e^(1)) = **0.2689**.
2. **Output unit, forward.** x₅ = 1 × 0.6225 + 1 × 0.2689 = **0.8914**, so y₅ = **0.7092**.
3. **Error.** E = ½ × (0.7092 − 1)² = **0.0423**.
4. **Backward at the output.**
   - ∂E/∂y₅ = 0.7092 − 1 = **−0.2908**.
   - ∂E/∂x₅ = −0.2908 × 0.7092 × (1 − 0.7092) = −0.2908 × 0.2062 = **−0.0600**.
5. **Derivatives of the top weights** (∂E/∂x₅ times the unit the weight comes from):
   - ∂E/∂w₅₃ = −0.0600 × 0.6225 = **−0.0373**.
   - ∂E/∂w₅₄ = −0.0600 × 0.2689 = **−0.0161**.
6. **Send the error down to the hidden units.**
   - ∂E/∂y₃ = −0.0600 × w₅₃ = −0.0600 × 1 = **−0.0600**; times y₃(1 − y₃) = 0.2350 gives
     ∂E/∂x₃ = **−0.0141**.
   - ∂E/∂y₄ = **−0.0600** in the same way; times y₄(1 − y₄) = 0.1966 gives ∂E/∂x₄ = **−0.0118**.
7. **Derivatives of the bottom weights.**
   - ∂E/∂w₃₁ = −0.0141 × y₁ = −0.0141 × 1 = **−0.0141**, and ∂E/∂w₄₁ = **−0.0118**.
   - ∂E/∂w₃₂ = −0.0141 × y₂ = −0.0141 × 0 = **0**: a weight from an input that was 0 does not
     change, and the same holds for w₄₂.
8. **Update with ε = 1.** Each weight minus its derivative:
   - w₅₃ = 1 + 0.0373 = **1.0373**; w₅₄ = 1 + 0.0161 = **1.0161**.
   - w₃₁ = 0.5 + 0.0141 = **0.5141**; w₄₁ = −1 + 0.0118 = **−0.9882**.
9. **Check.** The forward pass with the new weights gives y₃ = 0.6258, y₄ = 0.2713 and
   y₅ = **0.7160**, so E = **0.0403**, down from 0.0423.

**What this means:** the error at one output unit told every weight, even those into hidden
units, which way to move and by how much. Repeated over many cases and sweeps, these small steps
are how the hidden units come to represent features of the task.
