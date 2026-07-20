---
id: architecture-era/mlp-backprop
type: architecture-era
slug: mlp-backprop
schema_version: 1.0
title: "Multilayer Perceptron & Backpropagation"
name: "Multilayer Perceptron (Backpropagation)"
era: early
era_range: "1986–2011"
year: 1986
family: classical-nn
core_innovation: "Stacked non-linear layers trained end-to-end by propagating error gradients backward through the network."
description: "Hidden layers plus the backpropagation algorithm — the recipe that made deep networks trainable."
key_features: ["Hidden layers with non-linear activations", "Backpropagation via the chain rule", "Universal function approximation", "Gradient-descent training"]
key_models: ["NETtalk (1987)", "LeCun handwritten-digit nets (1989)"]
paper_title: "Learning Representations by Back-Propagating Errors"
paper_url: "https://doi.org/10.1038/323533a0"
influenced_by: [architecture-era/perceptron]
led_to: [architecture-era/cnn, architecture-era/rnn-lstm]

eli5: "A single neuron can only draw a straight line, which is too simple for most problems. So we stack layers of neurons — each layer bends the answer a little more — until the network can fit any shape. Backpropagation is the trick that tells every neuron, even the hidden ones deep inside, exactly how to change to make the final answer better."

analogy:
  title: "A kitchen brigade passing blame back down the line"
  body: "Picture a restaurant kitchen: prep cooks chop, line cooks assemble, the head chef plates — each stage feeds the next, and only the final plate is judged by the diner. When a dish comes back too salty, the head chef doesn't shrug; the complaint travels *backward* — the chef tells the line cook how much the saltiness was their doing, the line cook tells the prep cook how much was theirs, and each adjusts their own step in proportion to their share of the blame. Backpropagation is exactly this: the final error is measured once, then the chain rule hands each hidden neuron precisely how much it contributed, so every layer — not just the last — knows which way to adjust."

narrative:
  motivation: "The perceptron could only carve a single straight boundary, so it famously couldn't even learn XOR. Everyone knew the fix in principle — stack neurons into hidden layers to build curved, compositional boundaries — but stacking created a new problem: the inner neurons have no label to aim at. You know what the network *should* output, but not what any hidden unit should output. Without a target, how do you train the middle?"
  approach: "Two changes unlock it. First, swap the perceptron's hard step for a smooth activation (sigmoid or tanh) so the whole network becomes a differentiable function — small weight changes produce small, measurable output changes. Second, treat the whole network as one big composed function and ask calculus for the gradient of the error with respect to every weight."
  solution: "Backpropagation computes that gradient efficiently by running the chain rule backward. A forward pass produces a prediction and an error; then, starting from the output, the algorithm sweeps backward layer by layer, reusing each layer's computed error signal to get the previous layer's — so all the gradients for the entire network cost about as much as a single forward pass. Feed those gradients to gradient descent and every weight, hidden or not, moves to shrink the error."
  outcome: "This turned a pile of neurons into a trainable, differentiable machine. It solved XOR, and — because a single wide hidden layer is a universal approximator — it could in principle fit any continuous function. The forward-pass → loss → backward-pass → update loop became the template that every deep network since, from convolutional nets to transformers, still runs unchanged."

build_steps:
  - { title: "1. Stack the layers", detail: "Wire neurons into layers: an input layer, one or more hidden layers, and an output layer. Every neuron in a layer connects to every neuron in the next — a fully-connected (dense) network." }
  - { title: "2. Use smooth activations", detail: "After each layer's weighted sum, apply a smooth non-linearity (sigmoid, tanh, later ReLU). The non-linearity is what lets stacked layers represent curves; smoothness is what lets gradients flow." }
  - { title: "3. Forward pass", detail: "Feed an example in and compute layer by layer to the output. Save each layer's intermediate values — they are needed for the backward pass." }
  - { title: "4. Measure the loss", detail: "Compare the output to the true label with a loss function (squared error, cross-entropy). This single number is what the whole network will try to reduce." }
  - { title: "5. Backward pass (backprop)", detail: "Starting at the output, apply the chain rule to send an error signal backward through each layer, reusing the layer above's signal to compute this layer's gradients. One backward sweep yields the gradient for every weight." }
  - { title: "6. Update and repeat", detail: "Nudge every weight a small step in the direction that lowers the loss (w ← w − η·gradient), then repeat over many examples until the loss stops falling." }

key_equation:
  latex: "\\frac{\\partial L}{\\partial w^{(l)}} = \\frac{\\partial L}{\\partial a^{(l)}} \\cdot \\frac{\\partial a^{(l)}}{\\partial z^{(l)}} \\cdot \\frac{\\partial z^{(l)}}{\\partial w^{(l)}}"
  plain: "To find how a weight in layer l affects the final loss, multiply together three local rates of change: how the loss depends on the layer's output, how that output depends on its pre-activation, and how the pre-activation depends on the weight. Chaining these local derivatives is what lets error from the very end reach a weight buried deep inside."
  terms:
    - { symbol: "L", meaning: "the loss — the single number measuring how wrong the output is" }
    - { symbol: "w^(l)", meaning: "a weight in layer l — the parameter we want the gradient for" }
    - { symbol: "z^(l)", meaning: "the pre-activation — the weighted sum before the activation function" }
    - { symbol: "a^(l)", meaning: "the activation — the layer's output after applying the non-linearity" }
    - { symbol: "∂ / ∂", meaning: "a partial derivative — the local sensitivity of one quantity to another" }

strengths:
  - "Universal approximation — a wide enough hidden layer can represent any continuous function."
  - "Backprop computes all gradients in one backward pass, roughly as cheap as the forward pass."
  - "End-to-end and differentiable, so the whole network trains jointly by gradient descent."
  - "General-purpose — the same recipe applies to any differentiable architecture built since."

weaknesses:
  - "Sigmoid/tanh activations squash gradients, so deep stacks suffer vanishing gradients and train slowly."
  - "Universal approximation is an existence result — it says a fitting network exists, not that training will find it."
  - "Fully-connected layers ignore structure (spatial, sequential), needing huge parameter counts for images or sequences."
  - "Prone to overfitting and sensitive to initialization and learning rate without careful tuning."

diagram: |
  flowchart LR
    X["Input layer"] --> H1["Hidden layer 1"]
    H1 --> H2["Hidden layer 2"]
    H2 --> O["Output layer"]
    O --> L["Loss vs true label"]
    L -.->|"gradients flow back"| O
    O -.-> H2
    H2 -.-> H1
    H1 -.-> X
diagram_caption: "Solid arrows are the forward pass computing a prediction; dashed arrows are backpropagation carrying gradients backward to update every layer."

notable_models:
  - { name: "Rumelhart–Hinton–Williams net", year: 1986, note: "The Nature paper that popularized backprop and showed hidden layers learn useful internal representations." }
  - { name: "NETtalk", year: 1987, note: "Learned to pronounce English text from examples — a striking early demo that hidden layers capture structure." }
  - { name: "LeNet (LeCun digit nets)", year: 1989, note: "Backprop-trained networks that read handwritten digits, the seed of modern convolutional nets." }

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
updated: 2026-07-20
sources:
  - { url: "https://doi.org/10.1038/323533a0", title: "Learning Representations by Back-Propagating Errors (Rumelhart, Hinton & Williams, 1986, Nature)", trust: 0.9 }
---

## What Changed
The perceptron could only draw straight lines. Stacking neurons into hidden layers gives a network the
power to represent curved, compositional decision surfaces — a two-layer network with a non-linear
activation is a *universal approximator*. The missing piece was how to train the inner weights, which
have no direct target to compare against. Rumelhart, Hinton, and Williams popularised the answer:
**backpropagation**, the systematic application of the chain rule to compute the gradient of a scalar
loss with respect to every weight in one backward sweep. Crucially, a hidden unit needs a *smooth*
activation (sigmoid, tanh) rather than the perceptron's hard step so that gradients can flow. This
turned a network of neurons into a differentiable function optimisable by gradient descent, solved XOR,
and set the template — forward pass, loss, backward pass, update — that every deep model since follows.

## Key Ideas
- **Hidden layers** — intermediate non-linear representations let the network compose features and fit
  functions no single linear unit can.
- **Backpropagation** — reuse the chain rule to compute all weight gradients in a single backward pass,
  cost comparable to the forward pass; this efficiency is what makes deep training feasible.
- **Universal approximation** — a sufficiently wide one-hidden-layer network can approximate any
  continuous function on a compact domain (an existence result, not a training guarantee).
- **Differentiable activations** — replacing the step with sigmoid/tanh is what lets error signals
  propagate; the choice of activation shapes gradient flow and vanishing-gradient behaviour.
