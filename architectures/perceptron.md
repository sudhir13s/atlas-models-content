---
id: architecture-era/perceptron
type: architecture-era
slug: perceptron
schema_version: 3.1
title: Perceptron
name: Perceptron
era: early
era_range: 1958–1969
year: 1958
family: classical-nn
core_innovation: A network of threshold units whose connections to the response unit change with reinforcement, so that it learns a classification from examples instead of having it wired in.
description: A machine that decides yes or no by adding up weighted inputs and checking whether the total is above zero, and that learns its weights from examples by correcting them each time it answers wrongly.
key_features:
- Threshold units that fire when their summed input reaches a threshold
- Fixed, randomly connected association units
- Learned connections from association units to the response unit
- Error-correction training
- 'Convergence theorem: a solution is reached whenever one exists'
paper_title: 'The Perceptron: A Probabilistic Model for Information Storage and Organization in the Brain'
paper_url: https://doi.org/10.1037/h0042519
influenced_by: []
led_to:
- architecture-era/mlp-backprop
analogy:
  title: A loan officer who adjusts the scoring sheet after each mistake
  body: 'This is an analogy, not the mechanism. A loan officer scores an application by giving each factor a number of points, adding them up and approving only if the total is above a cutoff. When a decision turns out wrong, the officer changes the points: if a good applicant was refused, every factor that applicant had gains a point; if a bad applicant was approved, every factor they had loses a point. Right decisions change nothing. The factors are the perceptron''s inputs, the points are its weights, the cutoff is the threshold, and the point changes are the error-correction rule in the key equation below.'
narrative:
  motivation: '**How does a brain store what it has learned?** **Frank Rosenblatt (1958)** set out to answer this question with a hypothetical nervous system, or machine, which he called a perceptron.


    - **Three questions:** how information about the world is sensed, in what form it is stored, and how stored information influences recognition and behaviour; the paper deals mainly with the second and third.

    - **Two positions:** memory as coded images that could in principle be read back, or memory as new connections that make a response more likely. Rosenblatt takes the second, which he calls **connectionist**.

    - **The models of the day:** brain models built from symbolic logic, which he describes as logical contrivances for particular algorithms that fail in important respects to correspond to a biological system.

    - **His choice of language:** **probability theory** instead of symbolic logic, because only the gross organisation of a nervous system is known, not its exact wiring.'
  approach: '**A randomly wired network whose connections change with reinforcement.** The 1958 paper starts from assumptions drawn from the position of **Hebb, Hayek, Uttley and Ashby**, among them:


    - **Random at birth:** the connections used in learning are largely random, subject to few genetic constraints.

    - **Plastic:** after activity, the chance that one set of cells makes another respond can change.

    - **Similar stimuli, same cells:** with many stimuli, similar ones tend to form pathways to the same responding cells.

    - **Reinforcement:** positive or negative reinforcement can help or hinder the connections being formed.

    - **Three kinds of unit:** sensory points (**S-units**) on a retina, association cells (**A-units**) and response cells (**R-units**).'
  solution: '**Threshold units, and values that change on reinforcement.**


    - **Firing rule:** an A-unit fires, all or nothing, when the sum of its excitatory and inhibitory inputs **equals or exceeds its threshold θ**.

    - **What is learned:** each A-unit carries a **value**; reinforcement changes the values, and the response whose A-units send the stronger total wins.

    - **Three learning dynamics (1958):** in the **alpha** system an active cell gains value for every impulse; in **beta** each source-set gains at a constant rate; in **gamma** active cells gain at the expense of inactive ones.

    - **Elementary perceptron (1961):** in *Principles of Neurodynamics* the S-to-A connections are **fixed**, only the A-to-R connections are **variable**, and the single R-unit outputs **+1 or −1**.

    - **Error correction (1961):** reinforcement is **0 for a correct response** and has the sign of the error otherwise; the report proves that this procedure reaches a solution in **finite time** whenever one exists.'
  outcome: '**Simulated, built in hardware, and later surpassed by trained hidden layers.**


    - **Simulation:** Rosenblatt reports that simulations on the **IBM 704** at the Cornell Aeronautical Laboratory bore out the theory in all its main predictions (1958).

    - **Hardware:** the **Mark I** perceptron used **motor-driven potentiometers** as its integrators and a **large patch-panel** for its connections; the 1961 report calls both intolerable for very large systems.

    - **Limits he named:** recognising **relationships**, such as the object left of the square, was excessively difficult for the perceptron (1958).

    - **The next step:** **Rumelhart, Hinton and Williams (1986)** describe back-propagation as going beyond the **perceptron-convergence procedure** by training hidden units that build their own features. They cite **Minsky and Papert''s *Perceptrons* (1969)** for the proof that symmetry detection needs an intermediate layer.'
key_equation:
  latex: \hat{y} = \begin{cases} 1 & \text{if } \mathbf{w}^{\top}\mathbf{x} + b > 0 \\ 0 & \text{otherwise} \end{cases} \qquad \mathbf{w} \leftarrow \mathbf{w} + \eta\,(y - \hat{y})\,\mathbf{x}, \quad b \leftarrow b + \eta\,(y - \hat{y})
  plain: 'In words: multiply each input by its weight, add the bias, and answer 1 if the total is above zero, otherwise 0. Then compare the answer with the correct one. If they agree, y − ŷ is 0 and nothing changes. If the perceptron answered 0 but should have answered 1, every weight gains η times its input; if it answered 1 but should have answered 0, every weight loses η times its input. The worked example above runs this rule by hand until it makes no mistakes.'
  terms:
  - symbol: x
    meaning: the inputs, one number per input line; in Rosenblatt's elementary perceptron, the outputs of the association units, each 1 (active) or 0
  - symbol: w
    meaning: the weights, one per input; the only numbers that are learned (Rosenblatt calls them the values of the connections to the response unit)
  - symbol: b
    meaning: 'the bias: the weight of an extra input that is always 1, equivalent to a threshold of the opposite sign'
  - symbol: ŷ
    meaning: the perceptron's answer, 1 or 0 (Rosenblatt's 1961 response unit answers +1 or −1)
  - symbol: y
    meaning: the correct answer for this example, 1 or 0
  - symbol: η
    meaning: the size of each correction; 1 in the worked example (Rosenblatt's quantized procedure adds one unit of reinforcement)
strengths:
- '**Guaranteed to finish when a solution exists:** the 1961 report proves that error correction reaches a solution in **finite time**, from **any starting state** and in any order of examples, provided every example keeps recurring.'
- '**Learns from examples:** the classification is taught by reinforcement, not wired in; a correct response leaves the values **unchanged** and an error changes only the **active** units.'
- '**Memory is spread out:** removing part of the association system would not wipe out one association, the 1958 paper concludes, but would show up as a **general deficit** in all of them.'
- '**Few measurable parameters:** the 1958 theory predicts learning curves from **six** physical parameters, among them the number of excitatory and inhibitory connections per A-unit, the threshold and the numbers of A-units and R-units.'
weaknesses:
- '**Not every classification can be learned:** the convergence theorem needs a solution to **exist**. With more examples than association units, some classification has **no solution** (1961, Corollary 2).'
- '**One straight boundary:** a single threshold unit splits its inputs with one line (a plane in more dimensions), so it cannot compute **XOR**; the worked example shows why.'
- '**Features are not learned:** the association units'' input connections are **fixed**; Rumelhart, Hinton and Williams write that such feature analysers are **not true hidden units** because they do not learn representations.'
- '**Relations are out of reach:** Rosenblatt writes that recognising a **relationship** between stimuli, such as the pattern that appeared before the circle, generally becomes **excessively difficult** for the perceptron.'
- '**No step count you can rely on:** the 1961 report calls its upper bounds on the number of corrections **generally quite poor** as estimates of the actual number of steps.'
visual:
  kind: perceptron_training
  inputs:
  - - 0
    - 0
  - - 0
    - 1
  - - 1
    - 0
  - - 1
    - 1
  targets:
  - 0
  - 1
  - 1
  - 1
  rate: 1
  caption: 'The worked example''s training run on OR, computed from zero weights with η = 1: one panel per correction, with the boundary line after it. After the fourth correction the line separates the examples and training stops. The examples and starting weights are an example, not from a trained model.'
comparison:
  before: Logic-based brain models (before 1958)
  after: Perceptron (Rosenblatt)
  rows:
  - aspect: Language used to analyse the network
    before: Symbolic logic and Boolean algebra
    after: Probability theory, because only the gross organisation of the network is known
    source: https://homepages.math.uic.edu/~lreyzin/papers/rosenblatt58.pdf
  - aspect: How the network is wired
    before: Logical contrivances designed to perform particular algorithms
    after: Largely random connections, subject to few constraints
    source: https://homepages.math.uic.edu/~lreyzin/papers/rosenblatt58.pdf
  - aspect: Where a memory is kept
    before: 'Coded memory theories: a coded image that could in principle be read back'
    after: 'In connections: a stored preference for a particular response'
    source: https://homepages.math.uic.edu/~lreyzin/papers/rosenblatt58.pdf
  - aspect: How a stimulus is recognised
    before: 'Coded memory theories: matching the stimulus against the contents of storage'
    after: The new pathways activate the right response directly, with no separate matching step
    source: https://homepages.math.uic.edu/~lreyzin/papers/rosenblatt58.pdf
key_numbers:
  columns:
  - Quantity
  - Value
  - Where Rosenblatt gives it
  rows:
  - cells:
    - Stages in the simplified perceptron analysed in 1958
    - '3: retina (S), association units (A), responses (R)'
    - 1958, Fig. 2
    source: https://homepages.math.uic.edu/~lreyzin/papers/rosenblatt58.pdf
  - cells:
    - Physical parameters the theory predicts learning from
    - '6: excitatory and inhibitory connections per A-unit, A-unit threshold, share of R-units each A-unit connects to, number of A-units, number of R-units'
    - 1958, Conclusions
    source: https://homepages.math.uic.edu/~lreyzin/papers/rosenblatt58.pdf
  - cells:
    - Example learning experiment
    - 1,000 random stimuli; R1 reinforced for the first 500, R2 for the remaining 500
    - 1958, ideal environment
    source: https://homepages.math.uic.edu/~lreyzin/papers/rosenblatt58.pdf
  - cells:
    - Responses needed to tell 100 classes apart with binary coding
    - 7 response pairs, given an ideal set of binary characteristics
    - 1958, binary coding of responses
    source: https://homepages.math.uic.edu/~lreyzin/papers/rosenblatt58.pdf
  - cells:
    - Output of the elementary perceptron's response unit
    - +1 or −1
    - 1961, Fig. 5
    source: https://papers.baulab.info/papers/also/Rosenblatt-1961.pdf
  - cells:
    - Reinforcement for a correct response under error correction
    - '0'
    - 1961, Definition 41
    source: https://papers.baulab.info/papers/also/Rosenblatt-1961.pdf
misunderstandings:
- misunderstanding: The perceptron was invented as a pattern-recognition machine.
  truth: Rosenblatt's 1961 preface says a perceptron is **first and foremost a brain model**, not an invention for pattern recognition, and that the name was meant as a **generic name for a variety of theoretical nerve nets**. The 1958 paper appeared in *Psychological Review* and asks how the brain stores and uses information.
  source: https://papers.baulab.info/papers/also/Rosenblatt-1961.pdf
- misunderstanding: The 1958 paper introduced the rule w ← w + η(y − ŷ)x and proved that it converges.
  truth: The 1958 paper analyses a network of S-, A- and R-units with **probability theory** and changes the **values** of association cells under the alpha, beta and gamma systems and in bivalent systems with positive and negative reinforcement. The error-correction procedure for an elementary perceptron and its **convergence theorem** are set out in the **1961 report**, which says the first published proof was **not rigorous** and credits later forms to Block, Joseph, Kesten and others.
  source: https://papers.baulab.info/papers/also/Rosenblatt-1961.pdf
- misunderstanding: Trained long enough, a perceptron can learn any classification.
  truth: The convergence theorem applies only when a solution **exists**. The 1961 report shows that if there are more stimuli than association units, **some classification has no solution**, and that the chance a randomly chosen classification has one **approaches zero** as the number of stimuli grows.
  source: https://papers.baulab.info/papers/also/Rosenblatt-1961.pdf
- misunderstanding: A perceptron has only inputs and an output, with nothing in between.
  truth: 'Rosenblatt''s perceptrons have **association units** between the retina and the response units. What they lack is **learning** in that layer: the S-to-A connections are fixed, and only the A-to-R values change. Rumelhart, Hinton and Williams therefore call these units **feature analysers** rather than true hidden units.'
  source: https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf
diagram_svg: '<svg class="atlas-figure" viewBox="0 0 860 430" role="img" aria-label="A perceptron: inputs x1 to xn are multiplied by weights w1 to wn, summed with a bias b, and passed through a step function that outputs 1 or 0; when the output is wrong the weights move by eta times the error times the input. An inset shows the straight decision boundary separating two classes in two dimensions.">

  <defs><marker id="perceptron-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="perceptron-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>

  <text class="d-title" x="57" y="30">One unit: weighted sum, then a hard threshold</text>

  <path class="d-line-strong" d="M83 96 L266.8 188.3"/>

  <text class="d-math" x="160.2" y="126.8" text-anchor="middle">w₁</text>

  <path class="d-line-strong" d="M83 152 L264.7 194.1"/>

  <text class="d-math" x="159.3" y="161.7" text-anchor="middle">w₂</text>

  <path class="d-line-strong" d="M83 264 L265.2 207.7"/>

  <text class="d-math" x="159.5" y="232.3" text-anchor="middle">wₙ</text>

  <path class="d-line-strong" d="M83 326 L267.8 213.5"/>

  <text class="d-math" x="170.6" y="294.8" text-anchor="middle">b</text>

  <circle class="d-node d-role-input" cx="70" cy="96" r="13"/>

  <text class="d-note" x="70" y="99.5" text-anchor="middle">x₁</text>

  <circle class="d-node d-role-input" cx="70" cy="152" r="13"/>

  <text class="d-note" x="70" y="155.5" text-anchor="middle">x₂</text>

  <circle class="d-node d-role-input" cx="70" cy="264" r="13"/>

  <text class="d-note" x="70" y="267.5" text-anchor="middle">xₙ</text>

  <text class="d-title" x="70" y="214" text-anchor="middle">⋮</text>

  <circle class="d-node" cx="70" cy="326" r="13"/>

  <text class="d-note" x="70" y="329.5" text-anchor="middle">+1</text>

  <text class="d-note" x="70" y="356" text-anchor="middle">bias input</text>

  <circle class="d-node" cx="290" cy="200" r="26"/>

  <text class="d-title" x="290" y="205" text-anchor="middle">Σ</text>

  <text class="d-math" x="290" y="160" text-anchor="middle">z = w·x + b</text>

  <text class="d-title" x="290" y="248" text-anchor="middle">Weighted sum</text>

  <path class="d-line-strong" d="M316 200 H350" marker-end="url(#perceptron-arrow)"/>

  <text class="d-math" x="334" y="192" text-anchor="middle">z</text>

  <rect class="d-box" x="352" y="168" width="104" height="64" rx="5"/>

  <path class="d-line" d="M362 216 H446 M404 176 V222"/>

  <path class="d-line-strong" d="M364 216 H404 V184 H444"/>

  <text class="d-note" x="399" y="187" text-anchor="end">1</text>

  <text class="d-note" x="408" y="228">0</text>

  <text class="d-title" x="404" y="248" text-anchor="middle">Step activation</text>

  <text class="d-note" x="404" y="264" text-anchor="middle">1 if z &gt; 0, else 0</text>

  <path class="d-line-strong" d="M456 200 H523" marker-end="url(#perceptron-arrow)"/>

  <circle class="d-node d-role-output" cx="540" cy="200" r="15"/>

  <text class="d-label" x="540" y="204" text-anchor="middle">ŷ</text>

  <text class="d-title" x="540" y="248" text-anchor="middle">Output</text>

  <text class="d-note" x="540" y="264" text-anchor="middle">ŷ ∈ {0, 1}</text>

  <path class="d-line-accent" stroke-dasharray="5 4" d="M540 276 V400 H196 V318" marker-end="url(#perceptron-arrow-accent)"/>

  <text class="d-label" x="530" y="306" text-anchor="end">compare with</text>

  <text class="d-label" x="530" y="320" text-anchor="end">true label y</text>

  <text class="d-note" x="206" y="330">nudges every wᵢ and b</text>

  <text class="d-math" x="368" y="374" text-anchor="middle">w ← w + η(y − ŷ)x<tspan dx="28">b ← b + η(y − ŷ)</tspan></text>

  <text class="d-note" x="368" y="390" text-anchor="middle">learning rule: weights change only on a mistake (y − ŷ = ±1)</text>

  <rect class="d-frame" x="590" y="44" width="256" height="368" rx="8"/>

  <text class="d-title" x="718" y="68" text-anchor="middle">Decision boundary (n = 2)</text>

  <path class="d-line" d="M618 318 H832.24 M618 318 V103.75999999999999"/>

  <text class="d-math" x="832.24" y="334" text-anchor="end">x₁</text>

  <text class="d-math" x="618" y="95.75999999999999" text-anchor="middle">x₂</text>

  <path class="d-line-accent" d="M642.72 120.23999999999998 L824 301.52"/>

  <text class="d-math" x="671.2" y="122.23999999999998">w·x + b = 0</text>

  <path class="d-line-strong" d="M725.12 202.64 L756.02 171.73999999999998" marker-end="url(#perceptron-arrow)"/>

  <text class="d-math" x="754.02" y="163.73999999999998" text-anchor="middle">w</text>

  <circle class="d-node d-role-feedforward" cx="745.7" cy="140.8" r="5"/>

  <circle class="d-node d-role-feedforward" cx="782.8" cy="182.0" r="5"/>

  <circle class="d-node d-role-feedforward" cx="803.4" cy="132.6" r="5"/>

  <circle class="d-node d-role-feedforward" cx="766.3" cy="210.9" r="5"/>

  <circle class="d-node d-role-feedforward" cx="813.7" cy="231.5" r="5"/>

  <circle class="d-node d-role-feedforward" cx="819.9" cy="186.2" r="5"/>

  <rect class="d-node d-role-router" x="642.3" y="227.0" width="9" height="9"/>

  <rect class="d-node d-role-router" x="675.3" y="251.7" width="9" height="9"/>

  <rect class="d-node d-role-router" x="658.8" y="181.7" width="9" height="9"/>

  <rect class="d-node d-role-router" x="708.3" y="268.2" width="9" height="9"/>

  <rect class="d-node d-role-router" x="716.5" y="222.9" width="9" height="9"/>

  <rect class="d-node d-role-router" x="630.0" y="152.8" width="9" height="9"/>

  <rect class="d-node d-role-router" x="749.5" y="288.8" width="9" height="9"/>

  <text class="d-label" x="824" y="107.88" text-anchor="end">ŷ = 1</text>

  <text class="d-label" x="626.24" y="301.52">ŷ = 0</text>

  <circle class="d-node d-role-feedforward" cx="618" cy="352" r="5"/>

  <text class="d-note" x="630" y="355.5">class y = 1</text>

  <rect class="d-node d-role-router" x="713.5" y="347.5" width="9" height="9"/>

  <text class="d-note" x="730" y="355.5">class y = 0</text>

  <text class="d-note" x="718" y="384" text-anchor="middle">one straight line (a hyperplane in n-D):</text>

  <text class="d-note" x="718" y="398" text-anchor="middle">classes that no line separates (XOR) defeat it</text>

  </svg>

  '
diagram_caption: A perceptron weights its inputs, adds a bias and fires 1 only if the sum is positive; on a mistake it moves w by η(y − ŷ)x. Its decision boundary is one straight line, so it can only split linearly separable classes.
notable_models:
- name: Perceptron (Rosenblatt)
  year: 1958
  note: 'Published in Psychological Review as a probabilistic model of information storage in the brain: sensory points, randomly connected association cells and response cells, analysed with probability theory.'
- name: Elementary perceptron
  year: 1961
  note: 'Defined in Principles of Neurodynamics: fixed S-to-A connections, learned A-to-R connections and a single response unit answering +1 or −1, trained by error correction with a convergence theorem.'
- name: Mark I perceptron
  note: Rosenblatt's hardware perceptron at the Cornell Aeronautical Laboratory, which used motor-driven potentiometers as integrators and a large patch-panel for its connections.
resources:
- kind: paper
  title: 'The Perceptron: A Probabilistic Model (Rosenblatt, 1958)'
  url: https://doi.org/10.1037/h0042519
- kind: video
  title: But what is a neural network? (3Blue1Brown)
  url: https://www.youtube.com/watch?v=aircAruvnKk
- kind: tutorial
  title: Single-Layer Neural Networks and the Perceptron (Sebastian Raschka)
  url: https://sebastianraschka.com/Articles/2015_singlelayer_neurons.html
- kind: tutorial
  title: 'scikit-learn User Guide: Linear Models, Perceptron'
  url: https://scikit-learn.org/stable/modules/linear_model.html
learn_first:
- label: Vectors & dot products
  tag: Math
- label: Linear separability
  tag: Concept
- label: Weights, bias & thresholds
  tag: Concept
tags:
- perceptron
- classical-nn
- foundational
- linear-classifier
updated: '2026-11-23'
sources:
- url: https://homepages.math.uic.edu/~lreyzin/papers/rosenblatt58.pdf
  title: 'The Perceptron: A Probabilistic Model for Information Storage and Organization in the Brain (Rosenblatt, Psychological Review 65(6), 386-408, 1958; doi:10.1037/h0042519)'
  trust: 0.95
- url: https://papers.baulab.info/papers/also/Rosenblatt-1961.pdf
  title: 'Principles of Neurodynamics: Perceptrons and the Theory of Brain Mechanisms (Rosenblatt, Cornell Aeronautical Laboratory Report VG-1196-G-8, 15 March 1961; DTIC AD 256582)'
  trust: 0.9
- url: https://www.cs.toronto.edu/~hinton/absps/naturebp.pdf
  title: Learning Representations by Back-Propagating Errors (Rumelhart, Hinton & Williams, Nature 323, 533-536, 1986; doi:10.1038/323533a0)
  trust: 0.95
---

## What Changed

Before the perceptron, Rosenblatt writes, brain models were built from **symbolic logic**: wiring
diagrams designed to carry out a particular algorithm. The perceptron starts from **random
wiring** and lets experience change it.

How to read the table above:

- **Coded memory** is the idea that a memory is a stored image that could, in principle, be
  read back like a photographic negative; Rosenblatt rejects it.
- **Connectionist** memory, the position he takes, keeps information as a **preference for a
  response**, held in the strength of connections.
- **Probability theory** replaces logic because only the overall organisation of a nervous
  system is known; the theory predicts learning curves, not the fate of each wire.
- **Recognition without matching:** a stimulus travels along the pathways that learning has
  strengthened and switches on its response directly.

## Key Ideas

- **Threshold unit:** a unit fires when the sum of its inputs reaches a threshold; it answers
  yes or no, nothing in between.
- **Only the last connections learn:** the association units are wired at random and stay
  fixed; the connections from them to the response unit change.
- **Learn from mistakes:** a correct answer changes nothing; a wrong one moves every active
  connection towards the right answer.
- **A guarantee with a condition:** training ends in finite time **if** some setting of the
  connections classifies every example correctly.

## How It Works

The figure above is the **learning part** of a perceptron: one response unit and the
connections into it. Read it from left to right, then follow the dashed line back.

In Rosenblatt's machines the inputs **x** are not raw pixels: a retina of sensory points feeds
**association units** through fixed random connections, and their outputs, each 1 (active) or
0, are the inputs drawn here.

1. **Inputs x₁ to xₙ.** One number per input line; in the worked example below, 0 or 1.
2. **Weights w₁ to wₙ.** Each input is multiplied by its weight. The weights are the only
   numbers the perceptron learns.
3. **Bias input "+1".** An extra input that is always 1; its weight b moves the cutoff.
   Rumelhart, Hinton and Williams note that a bias is equivalent to a threshold of the
   opposite sign.
4. **Weighted sum Σ.** The products are added: z = w·x + b. This is one number, the
   perceptron's score for this example.
5. **Step activation.** The score becomes an answer: ŷ = 1 if z > 0, otherwise 0.
   - Rosenblatt's 1961 response unit answers **+1 or −1** instead of 1 or 0.
   - His error-correction procedure counts a score of exactly 0 as a **wrong** answer; the
     figure's rule answers 0, as the worked example does.
6. **Compare with the true label y (dashed line).** The error y − ŷ is 0 for a right answer,
   +1 if the perceptron should have said 1, and −1 if it should have said 0.
7. **Correct the weights.** Each weight changes by η × (y − ŷ) × its input, and b by
   η × (y − ŷ).
   - Inputs that were 0 do not change; inputs that were active move towards the right answer.
   - This matches the 1961 definition of error correction: zero reinforcement for a correct
     response, and otherwise reinforcement with the **sign of the error**.
8. **Repeat over the examples.** Training stops after a full pass with no mistakes.
9. **The boundary (inset).** The points where w·x + b = 0 form a straight line between the
   class drawn as dots and the class drawn as squares; training moves this line.

## Worked Example

**Teaching a perceptron OR by hand.** The four examples and the starting weights are made up so
every step can be checked: an example, not from a trained model.

The perceptron must answer 1 when at least one of its two inputs is 1. It starts with every
weight at 0, uses η = 1, and sees the examples in the order of the table, again and again.

| x₁ | x₂ | Correct answer y |
| --- | --- | --- |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

1. **Pass 1, example (0, 0).** z = 0 × 0 + 0 × 0 + 0 = **0**, so ŷ = 0. Correct; nothing changes.
2. **Pass 1, example (0, 1).** z = **0**, so ŷ = 0, but y = 1. The error is 1 − 0 = **+1**:
   - w₁ = 0 + 1 × 1 × 0 = 0; w₂ = 0 + 1 × 1 × 1 = 1; b = 0 + 1 × 1 = 1.
   - Now **w = (0, 1), b = 1**.
3. **Pass 1, examples (1, 0) and (1, 1).** z = 0 + 0 + 1 = **1** and z = 0 + 1 + 1 = **2**;
   both answers are 1. Correct.
4. **Pass 2, example (0, 0).** z = 0 + 0 + 1 = **1**, so ŷ = 1, but y = 0. The error is **−1**:
   - Both inputs are 0, so w stays (0, 1); b = 1 − 1 = 0.
   - Now **w = (0, 1), b = 0**.
5. **Pass 2, example (0, 1).** z = 1, ŷ = 1. Correct.
6. **Pass 2, example (1, 0).** z = 0 × 1 + 1 × 0 + 0 = **0**, so ŷ = 0, but y = 1. Error **+1**:
   - w₁ = 0 + 1 = 1; w₂ stays 1, because x₂ = 0; b = 0 + 1 = 1.
   - Now **w = (1, 1), b = 1**. Example (1, 1) then gives z = 3, ŷ = 1. Correct.
7. **Pass 3, example (0, 0).** z = **1**, ŷ = 1, but y = 0. Error **−1**: b = 1 − 1 = 0, and
   w stays (1, 1). The other three examples give z = 1, 1 and 2. Correct.
8. **Pass 4: no mistakes.** With **w = (1, 1), b = 0** the scores are 0, 1, 1 and 2, so the
   answers are 0, 1, 1 and 1, exactly OR. Training stops after **4 corrections**.

**Why XOR cannot be taught.** XOR wants 1 for (0, 1) and (1, 0) and 0 for (0, 0) and (1, 1).

- (0, 1) and (1, 0) need w₂ + b > 0 and w₁ + b > 0. Adding them: **w₁ + w₂ + 2b > 0**.
- (0, 0) and (1, 1) need b ≤ 0 and w₁ + w₂ + b ≤ 0. Adding them: **w₁ + w₂ + 2b ≤ 0**.
- Both cannot hold, so no weights exist. The convergence theorem then promises nothing, and
  the corrections never stop.

**What this means:** each mistake moved the boundary line towards the example it got wrong,
and after four corrections the line separated the two classes. For XOR no single line does, so
a perceptron alone cannot learn it.
