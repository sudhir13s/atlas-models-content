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
  - { title: "2. Compute a residual F(x)", detail: "Pass x through a couple of weight layers (convolution → batch normalization → ReLU, then convolution → batch normalization; the second ReLU waits until after the addition). This branch learns F(x), the correction to apply — the 'red-pen edits'." }
  - { title: "3. Carry x along the shortcut", detail: "In parallel, route the original x straight past those layers untouched. If the shapes match this is a plain identity wire; if channels or resolution change, a 1×1 convolution reshapes it." }
  - { title: "4. Add them: F(x) + x", detail: "Element-wise add the residual branch and the shortcut. The block outputs the input plus its learned correction, so 'do nothing' means simply learning F ≈ 0." }
  - { title: "5. Apply the final nonlinearity", detail: "A ReLU after the addition keeps the network nonlinear while preserving the additive shortcut that carries gradients backward cleanly." }
  - { title: "6. Stack dozens of blocks", detail: "Chain these blocks into very deep networks. ResNet-18 and -34 stack this two-layer block; ResNet-50, -101 and -152 use a three-layer bottleneck version (1×1, 3×3, 1×1 convolutions) around the same shortcut. Because each addition is a gradient highway, even 100+ layers train stably." }

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

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 880 500" role="img" aria-label="A residual block: the input x passes through two 3 by 3 convolution layers with batch normalization to give F(x), while an identity shortcut carries x around both layers to an addition, and a ReLU follows the sum F(x) + x. Beside it, ResNet-34 stacks 3, 4, 6 and 3 such blocks in stages of 64, 128, 256 and 512 channels, halving the spatial size between stages.">
  <defs><marker id="resnet-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="resnet-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="resnet-arrow-thin" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="6" markerHeight="6" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <text class="d-title" x="40" y="30">Residual block (basic block, ResNet-18/34)</text>
  <text class="d-math" x="190" y="62" text-anchor="middle">x</text>
  <path class="d-line-strong" d="M190 70 V116" marker-end="url(#resnet-arrow)"/>
  <circle class="d-node" cx="190" cy="92" r="3"/>
  <rect class="d-box d-role-feedforward" x="115" y="118" width="150" height="46" rx="5"/>
  <text class="d-title" x="190" y="137" text-anchor="middle">weight layer: 3×3 conv</text>
  <text class="d-note" x="190" y="153" text-anchor="middle">→ batch norm → ReLU</text>
  <path class="d-line-strong" d="M190 164 V202" marker-end="url(#resnet-arrow)"/>
  <rect class="d-box d-role-feedforward" x="115" y="204" width="150" height="46" rx="5"/>
  <text class="d-title" x="190" y="223" text-anchor="middle">weight layer: 3×3 conv</text>
  <text class="d-note" x="190" y="239" text-anchor="middle">→ batch norm (no ReLU yet)</text>
  <path class="d-line-strong" d="M190 250 V285" marker-end="url(#resnet-arrow)"/>
  <path class="d-line" d="M105 118 H99 V250 H105"/>
  <text class="d-math" x="91" y="180" text-anchor="end">F(x)</text>
  <text class="d-note" x="91" y="194" text-anchor="end">residual</text>
  <circle class="d-node d-role-attention" cx="190" cy="300" r="13"/>
  <path class="d-line-strong" d="M184 300 H196 M190 294 V306"/>
  <path class="d-line-strong" d="M190 313 V336" marker-end="url(#resnet-arrow)"/>
  <text class="d-math" x="180" y="329.5" text-anchor="end">F(x) + x</text>
  <rect class="d-box" x="150" y="338" width="80" height="28" rx="5"/>
  <text class="d-title" x="190" y="356" text-anchor="middle">ReLU</text>
  <path class="d-line-strong" d="M190 366 V390" marker-end="url(#resnet-arrow)"/>
  <text class="d-math" x="190" y="404" text-anchor="middle">y = ReLU(F(x) + x)</text>
  <path class="d-line-accent" d="M190 92 H312 Q330 92 330 110 V282 Q330 300 312 300 H205" marker-end="url(#resnet-arrow-accent)"/>
  <text class="d-title" x="340" y="190">identity</text>
  <text class="d-title" x="340" y="204">shortcut</text>
  <text class="d-math" x="340" y="220">x, no weights</text>
  <text class="d-note" x="40" y="470">If the best block is “do nothing”, the layers only need F(x) → 0;</text>
  <text class="d-note" x="40" y="484">gradients also flow back through the + unchanged.</text>
  <rect class="d-frame" x="458" y="14" width="408" height="472" rx="8"/>
  <text class="d-title" x="478" y="40">ResNet-34: 16 blocks in 4 stages (224×224 input)</text>
  <rect class="d-box d-role-input" x="606" y="84" width="232" height="24" rx="4"/>
  <text class="d-label" x="722" y="100" text-anchor="middle">7×7 conv 64, /2 → 3×3 max pool, /2</text>
  <text class="d-title" x="478" y="90">stem</text>
  <text class="d-note" x="478" y="104">224 → 56×56</text>
  <text class="d-title" x="478" y="150">conv2_x</text>
  <text class="d-note" x="478" y="164">3 blocks, 64 ch</text>
  <text class="d-note" x="478" y="176">56×56</text>
  <path class="d-line" d="M722 108 V127 H592 V156 H604" marker-end="url(#resnet-arrow-thin)"/>
  <rect class="d-box d-role-feedforward" x="606" y="149" width="28" height="14" rx="3"/>
  <path class="d-line-accent" d="M601 156 C601 134, 639 134, 639 156"/>
  <path class="d-line" d="M634 156 H646"/>
  <rect class="d-box d-role-feedforward" x="646" y="149" width="28" height="14" rx="3"/>
  <path class="d-line-accent" d="M641 156 C641 134, 679 134, 679 156"/>
  <path class="d-line" d="M674 156 H686"/>
  <rect class="d-box d-role-feedforward" x="686" y="149" width="28" height="14" rx="3"/>
  <path class="d-line-accent" d="M681 156 C681 134, 719 134, 719 156"/>
  <text class="d-title" x="478" y="216">conv3_x</text>
  <text class="d-note" x="478" y="230">4 blocks, 128 ch</text>
  <text class="d-note" x="478" y="242">28×28</text>
  <path class="d-line" d="M700 163 V190 H592 V222 H604" marker-end="url(#resnet-arrow-thin)"/>
  <rect class="d-box d-role-feedforward" x="606" y="212" width="28" height="20" rx="3"/>
  <path class="d-line-accent" stroke-dasharray="3 2" d="M601 222 C601 197, 639 197, 639 222"/>
  <path class="d-line" d="M634 222 H646"/>
  <rect class="d-box d-role-feedforward" x="646" y="212" width="28" height="20" rx="3"/>
  <path class="d-line-accent" d="M641 222 C641 197, 679 197, 679 222"/>
  <path class="d-line" d="M674 222 H686"/>
  <rect class="d-box d-role-feedforward" x="686" y="212" width="28" height="20" rx="3"/>
  <path class="d-line-accent" d="M681 222 C681 197, 719 197, 719 222"/>
  <path class="d-line" d="M714 222 H726"/>
  <rect class="d-box d-role-feedforward" x="726" y="212" width="28" height="20" rx="3"/>
  <path class="d-line-accent" d="M721 222 C721 197, 759 197, 759 222"/>
  <text class="d-title" x="478" y="284">conv4_x</text>
  <text class="d-note" x="478" y="298">6 blocks, 256 ch</text>
  <text class="d-note" x="478" y="310">14×14</text>
  <path class="d-line" d="M740 232 V255 H592 V290 H604" marker-end="url(#resnet-arrow-thin)"/>
  <rect class="d-box d-role-feedforward" x="606" y="277" width="28" height="26" rx="3"/>
  <path class="d-line-accent" stroke-dasharray="3 2" d="M601 290 C601 262, 639 262, 639 290"/>
  <path class="d-line" d="M634 290 H646"/>
  <rect class="d-box d-role-feedforward" x="646" y="277" width="28" height="26" rx="3"/>
  <path class="d-line-accent" d="M641 290 C641 262, 679 262, 679 290"/>
  <path class="d-line" d="M674 290 H686"/>
  <rect class="d-box d-role-feedforward" x="686" y="277" width="28" height="26" rx="3"/>
  <path class="d-line-accent" d="M681 290 C681 262, 719 262, 719 290"/>
  <path class="d-line" d="M714 290 H726"/>
  <rect class="d-box d-role-feedforward" x="726" y="277" width="28" height="26" rx="3"/>
  <path class="d-line-accent" d="M721 290 C721 262, 759 262, 759 290"/>
  <path class="d-line" d="M754 290 H766"/>
  <rect class="d-box d-role-feedforward" x="766" y="277" width="28" height="26" rx="3"/>
  <path class="d-line-accent" d="M761 290 C761 262, 799 262, 799 290"/>
  <path class="d-line" d="M794 290 H806"/>
  <rect class="d-box d-role-feedforward" x="806" y="277" width="28" height="26" rx="3"/>
  <path class="d-line-accent" d="M801 290 C801 262, 839 262, 839 290"/>
  <text class="d-title" x="478" y="356">conv5_x</text>
  <text class="d-note" x="478" y="370">3 blocks, 512 ch</text>
  <text class="d-note" x="478" y="382">7×7</text>
  <path class="d-line" d="M820 303 V324 H592 V362 H604" marker-end="url(#resnet-arrow-thin)"/>
  <rect class="d-box d-role-feedforward" x="606" y="346" width="28" height="32" rx="3"/>
  <path class="d-line-accent" stroke-dasharray="3 2" d="M601 362 C601 331, 639 331, 639 362"/>
  <path class="d-line" d="M634 362 H646"/>
  <rect class="d-box d-role-feedforward" x="646" y="346" width="28" height="32" rx="3"/>
  <path class="d-line-accent" d="M641 362 C641 331, 679 331, 679 362"/>
  <path class="d-line" d="M674 362 H686"/>
  <rect class="d-box d-role-feedforward" x="686" y="346" width="28" height="32" rx="3"/>
  <path class="d-line-accent" d="M681 362 C681 331, 719 331, 719 362"/>
  <path class="d-line" d="M700 378 V410 H592 V432 H604" marker-end="url(#resnet-arrow-thin)"/>
  <rect class="d-box d-role-output" x="606" y="420" width="232" height="24" rx="4"/>
  <text class="d-label" x="722" y="436" text-anchor="middle">global avg pool → fc 1000 → softmax</text>
  <text class="d-title" x="478" y="426">head</text>
  <text class="d-note" x="478" y="440">7×7 → class</text>
  <rect class="d-box d-role-feedforward" x="478" y="467" width="14" height="10" rx="2"/>
  <path class="d-line-accent" d="M474 472 C474 457, 496 457, 496 472"/>
  <text class="d-note" x="504" y="476">block + shortcut; taller = wider</text>
  <path class="d-line-accent" stroke-dasharray="3 2" d="M714 472 C714 457, 736 457, 736 472"/>
  <text class="d-note" x="742" y="476">stride 2: size /2, ch ×2</text>
  </svg>
diagram_caption: "A residual block adds its input x back to what two 3×3 conv layers compute, so they learn only the correction F(x); ResNet-34 stacks 3, 4, 6 and 3 such blocks at 64 to 512 channels, halving the map size between stages."
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
