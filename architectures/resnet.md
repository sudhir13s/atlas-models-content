---
id: architecture-era/resnet
type: architecture-era
slug: resnet
schema_version: 1.0
title: "Residual Networks (ResNet)"
name: "ResNet"
era: foundation
era_range: "2015–2018"
year: 2015
family: vision
core_innovation: "Residual skip connections let layers learn a correction to the identity, making networks of 100+ layers trainable."
description: "The skip-connection idea that broke the depth barrier and became a universal deep-learning building block."
key_features: ["Identity skip connections", "Residual (delta) learning", "Very deep networks (50–1000+ layers)", "Improved gradient flow", "Batch normalization"]
key_models: ["ResNet-50/101/152 (2015)", "ResNeXt (2016)", "Wide ResNet (2016)"]
paper_title: "Deep Residual Learning for Image Recognition"
paper_url: "https://arxiv.org/abs/1512.03385"
influenced_by: [architecture-era/cnn]
led_to: [architecture-era/transformer, architecture-era/vision-transformer]

eli5: "Very deep networks were surprisingly hard to train — piling on layers made them worse, not better. ResNet added a shortcut that lets each layer just tweak what came before instead of rebuilding it from scratch. That one shortcut made networks hundreds of layers deep suddenly easy to train."

analogy:
  title: "Edits on a draft, not a rewrite from scratch"
  body: "Imagine passing a document down a line of editors. If each editor must rewrite the whole page from memory, errors pile up and by the tenth editor the text is garbled — that was a plain deep network. Now give each editor the *original page* plus a red pen: they only mark the changes, and their edits are added on top of the original. If an editor has nothing to improve, they hand the page along untouched. That 'original page carried forward' is the skip connection, and 'only mark the changes' is residual learning — the network learns a small correction, not a full rebuild."

narrative:
  motivation: "By 2015 depth was the frontier: deeper convolutional networks kept winning, so surely deeper still would win more. In theory a deep network can never be worse than a shallow one — it could set the extra layers to do nothing (the identity) and match it. But experiments showed the opposite: past a point, adding layers made *both* training and test error rise. This was not overfitting — training error itself got worse. Something about optimization was breaking."
  approach: "He et al. reframed the problem. If the ideal behaviour of a block is close to 'pass the input through unchanged', then a plain stack of layers has to laboriously learn the identity function — and it turns out that is hard for them. So instead of asking a block to output the desired mapping H(x), ask it to output only the *difference* from the input: the residual F(x) = H(x) − x. Then wire the input straight to the output with a shortcut."
  solution: "The residual block computes F(x) with a couple of convolutional layers, then *adds the untouched input back*: output = F(x) + x. Learning 'do nothing' is now trivial — just drive F toward zero. The shortcut has a second, crucial benefit: during backpropagation, gradients flow straight through the addition unchanged, giving them an uninterrupted highway back to early layers and defeating the vanishing-gradient problem that had capped depth."
  outcome: "ResNet trained networks 100+ (even 1000+) layers deep, won the ImageNet 2015 competition by a wide margin, and set new records across vision tasks. But its real legacy is the primitive: the residual connection is now in almost every deep architecture. Every sublayer of every Transformer — and therefore every large language model and the Vision Transformer — is wrapped in a residual connection inherited directly from this paper."

build_steps:
  - { title: "1. Take the block's input x", detail: "A residual block receives the feature map produced by the previous block. Call it x — this is the signal we want to refine, not replace." }
  - { title: "2. Compute a residual F(x)", detail: "Pass x through a couple of weight layers (convolution → batch normalization → ReLU, twice). This branch learns F(x), the correction to apply — the 'red-pen edits'." }
  - { title: "3. Carry x along the shortcut", detail: "In parallel, route the original x straight past those layers untouched. If the shapes match this is a plain identity wire; if channels or resolution change, a 1×1 convolution reshapes it." }
  - { title: "4. Add them: F(x) + x", detail: "Element-wise add the residual branch and the shortcut. The block outputs the input plus its learned correction, so 'do nothing' means simply learning F ≈ 0." }
  - { title: "5. Apply the final nonlinearity", detail: "A ReLU after the addition keeps the network nonlinear while preserving the additive shortcut that carries gradients backward cleanly." }
  - { title: "6. Stack dozens of blocks", detail: "Chain these blocks into very deep networks (ResNet-50, -101, -152). Because each addition is a gradient highway, even 100+ layers train stably." }

key_equation:
  latex: "\\mathbf{y} = \\mathcal{F}(\\mathbf{x}, \\{W_i\\}) + \\mathbf{x}"
  plain: "The block's output is its input plus a learned correction. F is what the weight layers compute (the residual); adding x back means the layers only need to learn how the output should *differ* from the input. If the best move is to change nothing, the network just pushes F toward zero — far easier than learning the identity from scratch."
  terms:
    - { symbol: "y", meaning: "the block's output feature map" }
    - { symbol: "x", meaning: "the block's input, carried forward unchanged by the skip connection" }
    - { symbol: "F(x, {Wᵢ})", meaning: "the residual — the correction the weight layers learn to add" }
    - { symbol: "{Wᵢ}", meaning: "the learnable weights inside the residual branch" }

strengths:
  - "Makes very deep networks (100–1000+ layers) trainable, breaking the depth barrier that stalled plain CNNs."
  - "The additive shortcut is a gradient highway — backprop reaches early layers undiminished, defeating vanishing gradients."
  - "Learning 'do nothing' is trivial (F → 0), so extra depth never hurts and often helps."
  - "The residual block is a drop-in primitive reused across nearly all modern architectures, including every Transformer."

weaknesses:
  - "More depth means more compute and memory; the deepest variants are expensive to train and run."
  - "Beyond a point, extra layers give diminishing returns — depth alone is not free accuracy."
  - "Skip connections that change shape need extra projection layers, adding design complexity."
  - "Residual blocks were tuned for convolutional vision; naive reuse elsewhere still needs careful normalization choices."

diagram: |
  flowchart TD
    X["Input x"] --> W1["Conv + BatchNorm + ReLU"]
    W1 --> W2["Conv + BatchNorm"]
    X -->|"identity shortcut"| ADD["＋ add"]
    W2 --> ADD
    ADD --> RELU["ReLU"]
    RELU --> Y["Output y = F(x) + x"]
diagram_caption: "A residual block: the weight layers learn a correction F(x) while the shortcut carries the input straight to the addition, so gradients and signal both flow cleanly."

notable_models:
  - { name: "ResNet-50/101/152", year: 2015, note: "Proved that with skip connections, deeper is genuinely better — won ImageNet 2015 with networks an order of magnitude deeper than before." }
  - { name: "Wide ResNet", year: 2016, note: "Showed that widening residual blocks can beat making them deeper, trading depth for width." }
  - { name: "ResNeXt", year: 2016, note: "Added parallel 'cardinality' paths inside each block, boosting accuracy without more depth or width." }
  - { name: "DenseNet", year: 2017, note: "Pushed skip connections to the limit — every layer connects to every later one — confirming shortcuts as a core idea." }

resources:
  - { kind: paper, title: "Deep Residual Learning for Image Recognition (He et al., 2015)", url: "https://arxiv.org/abs/1512.03385" }
  - { kind: course, title: "CS231n: Training Neural Networks & Architectures (Stanford)", url: "https://cs231n.github.io/convolutional-networks/#architectures" }
  - { kind: video, title: "ResNet Explained — Deep Residual Learning (Yannic Kilcher)", url: "https://www.youtube.com/watch?v=GWt6Fu05voI" }
  - { kind: blog, title: "An Overview of ResNet and its Variants (Towards Data Science, open)", url: "https://towardsdatascience.com/an-overview-of-resnet-and-its-variants-5281e2f56035/" }

learn_first:
  - { label: "Convolutional neural networks", href: "/models/architecture/cnn", tag: "Architecture" }
  - { label: "Backpropagation & the vanishing-gradient problem", href: "/learn/concepts/backpropagation", tag: "Math" }
  - { label: "Batch normalization", href: "/learn/concepts/batch-normalization", tag: "Concept" }

tags: [resnet, vision, residual, skip-connection, deep-networks]
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (He et al., 2015)", trust: 0.95 }
  - { url: "https://cs231n.github.io/convolutional-networks/#architectures", title: "CS231n Architectures (Stanford)", trust: 0.9 }
---

## What Changed
Deeper networks should be at least as good as shallower ones — a deep net can always set extra layers
to identity — yet in practice, past a point, adding layers made both training *and* test error worse.
This "degradation" problem, not overfitting, capped useful depth. He et al. fixed it with a tiny
structural change: the **residual block**, where a layer computes $\mathcal{F}(x)$ and the block
outputs $\mathcal{F}(x) + x$ via a skip connection. Now a layer only has to learn the *residual* (the
correction to the identity), and if the best thing to do is nothing, driving $\mathcal{F}$ toward zero
is easy. Practically, the skip is also a gradient highway: backprop flows through the identity path
undiminished, defeating the vanishing-gradient problem. ResNet trained networks of 100+ (even 1000+)
layers, won ImageNet 2015, and its skip connection became one of the most reused primitives in all of
deep learning — the transformer's residual-around-every-sublayer design (and thus ViT) inherits it
directly.

## Key Ideas
- **Residual learning** — parameterise a block as identity plus a learned delta $\mathcal{F}(x)+x$, so
  layers refine rather than replace the signal.
- **Degradation, not overfitting** — the pre-ResNet failure of deep nets was an optimization problem;
  skip connections make the identity easy to represent and recover trainability.
- **Gradient highway** — the additive shortcut passes gradients backward unattenuated, so very deep
  stacks stay trainable.
- **A universal primitive** — residual connections now appear in nearly every deep architecture,
  including every transformer sublayer.
