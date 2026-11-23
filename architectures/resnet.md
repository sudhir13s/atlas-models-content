---
id: architecture-era/resnet
type: architecture-era
slug: resnet
schema_version: 2.5
title: "Residual Networks (ResNet)"
name: "ResNet"
era: foundation
era_range: "2015–2018"
year: 2015
family: vision
core_innovation: "Residual skip connections let layers learn a correction to the identity, making networks of 100+ layers trainable."
description: "A deep image-recognition network built from blocks that add their input back to their output, so the layers inside each block only have to learn a correction to what they receive."
key_features: ["Identity skip connections", "Residual (delta) learning", "Very deep networks (up to 152 layers on ImageNet)"]
key_models: ["ResNet-50/101/152 (2015)", "Wide ResNet (2016)", "ResNeXt (2016)"]
paper_title: "Deep Residual Learning for Image Recognition"
paper_url: "https://arxiv.org/abs/1512.03385"
influenced_by: [architecture-era/cnn]
led_to: [architecture-era/transformer, architecture-era/vision-transformer]

analogy:
  title: "Edits on a draft, not a rewrite from scratch"
  body: "This is an analogy, not the mechanism. Picture a document passed down a line of editors. If each editor had to rewrite the whole page from memory, mistakes would pile up along the line. Now give each editor the original page and a red pen: they only mark changes, and the changes are applied on top of the page they received. An editor with nothing to improve hands the page on untouched. The page carried forward is the shortcut connection, and the red-pen marks are the residual that the layers learn. The exact rule is the key equation below."

narrative:
  motivation: "**Deeper networks were getting worse, and not from overfitting.** The paper is by **He, Zhang, Ren and Sun (2015)** of Microsoft Research.\n\n- **Depth mattered:** the leading ImageNet results of the time used networks of **16 to 30** layers.\n- **Vanishing gradients largely handled:** the authors write that normalized initialisation and normalization layers such as **batch normalization** already let networks of tens of layers start converging.\n- **The degradation problem:** as depth grows, accuracy saturates and then degrades; a **34-layer** plain network has higher **training** error than an **18-layer** one throughout training.\n- **Why that is odd:** a deeper network could copy the shallower one and make its extra layers pass their input through unchanged, so it should never have higher training error; the authors report that current solvers fail to find solutions as good, or cannot within feasible time."
  approach: "**Let the layers learn only the change.** If a few stacked layers should compute some mapping **H(x)**, the authors let them fit **F(x) = H(x) − x** and add x back.\n\n- **Hypothesis:** the authors hypothesise that the residual F is **easier to optimise** than the original mapping.\n- **The extreme case:** if passing the input through unchanged were optimal, it would be easier to push the residual to **zero** than to fit that identity with a stack of nonlinear layers.\n- **Shortcut connections:** the addition is made by a connection that **skips layers** and copies its input; it adds **neither parameters nor computation**.\n- **Not a gate:** concurrent **highway networks** used shortcuts with data-dependent gates that have parameters and can close; ResNet's identity shortcuts are **never closed**."
  solution: "**Stack residual blocks and keep VGG's simple design rules.**\n\n- **Block:** two **3 × 3** convolutions with batch normalization after each, the input added back, then a **ReLU** (Figure 2 of the paper).\n- **Design rules:** layers that produce maps of the same size have the same number of filters; when the map size is **halved**, the number of filters is **doubled**.\n- **Shape changes:** where the number of channels grows, the shortcut either pads the new channels with **zeros** (option A) or uses a **1 × 1 projection** (option B).\n- **Deeper models:** ResNet-50, -101 and -152 replace each block by a three-layer **bottleneck** (1 × 1, 3 × 3, 1 × 1 convolutions).\n- **Cost:** the 34-layer network needs **3.6 billion** FLOPs (multiply-adds), **18%** of VGG-19's **19.6 billion**."
  outcome: "**Depth started to pay.**\n\n- **ImageNet:** the **152-layer** ResNet reaches **4.49%** top-5 validation error as a single model; an ensemble of **six** models reaches **3.57%** top-5 error on the test set and won **1st place** in ILSVRC 2015 classification.\n- **Other tasks:** replacing VGG-16 with ResNet-101 in the Faster R-CNN detector gives a **28%** relative improvement on COCO; the team also won ImageNet detection and localization and COCO detection and segmentation in 2015.\n- **CIFAR-10:** the authors trained a **110-layer** network to **6.43%** test error and explored one of **1202** layers.\n- **Follow-up (2016):** **He et al.** moved batch normalization and ReLU in front of the weight layers (pre-activation) and report **4.62%** CIFAR-10 error with a **1001-layer** ResNet.\n- **Transformer (2017):** wraps each of its sub-layers in a **residual connection**, citing this paper."

key_equation:
  latex: "\\mathbf{y} = \\mathcal{F}(\\mathbf{x}, \\{W_i\\}) + \\mathbf{x}"
  plain: "In words: the block's output y is its input x plus what its weight layers compute, F. The layers only have to learn how the output should differ from the input; if nothing should change, they only need F close to zero. When x and F have different shapes, the paper replaces x by a projection Wₛx. The worked example above computes one block by hand."
  terms:
    - { symbol: "y", meaning: "the block's output; in the paper's block a ReLU is applied to it before the next block" }
    - { symbol: "x", meaning: "the block's input, carried to the addition unchanged by the shortcut" }
    - { symbol: "F(x, {Wᵢ})", meaning: "the residual: what the block's two or three weight layers compute, written F = W₂σ(W₁x) for two layers, with σ the ReLU and biases left out" }
    - { symbol: "{Wᵢ}", meaning: "the learned weights of the block's layers; the identity shortcut itself has none" }

strengths:
  - "**Depth helps instead of hurting:** the **34-layer** ResNet has **25.03%** top-1 validation error against **27.88%** for the 18-layer one, while the plain 34-layer network is worse than the plain 18-layer one (**28.54%** against **27.94%**)."
  - "**No extra cost:** identity shortcuts add **neither parameters nor computation** apart from an element-wise addition, so the plain and residual 34-layer networks both need **3.6 billion** FLOPs."
  - "**Deep but cheaper than VGG:** the authors report the **152-layer** ResNet at **11.3 billion** FLOPs, below VGG-16 and VGG-19 at **15.3** and **19.6 billion**."
  - "**Learned features transfer:** replacing VGG-16 with ResNet-101 in Faster R-CNN raises COCO mAP@[.5, .95] by **6.0** points, which the authors give as a **28%** relative improvement."
  - "**Faster early training:** at 18 layers, where plain and residual nets are about equally accurate, the authors report that the ResNet **converges faster**."
  - "**A direct path for gradients:** **He et al. (2016)** show that when the shortcut and the step after the addition are both identities, the gradient reaching a layer contains a term that does **not vanish** even when the weights are arbitrarily small."

weaknesses:
  - "**Too deep can overfit:** on CIFAR-10 the **1202-layer** network (19.4M parameters) has **7.93%** test error against **6.43%** for the 110-layer one, which the authors attribute to **overfitting**."
  - "**Diminishing returns:** **Zagoruyko and Komodakis (2016)** write that each fraction of a percent of accuracy costs **nearly doubling** the number of layers, which makes very deep residual networks slow to train."
  - "**Shape changes need special shortcuts:** where channels grow, the shortcut needs **zero-padding** or a **1 × 1 projection**; the authors argue that the zero-padded channels get **no residual learning**."
  - "**Projections are costly in bottlenecks:** replacing the identity shortcut of a bottleneck block with a projection would, the authors write, **double** the block's time complexity and model size."
  - "**The ReLU after the addition gets in the way:** **He et al. (2016)** report that a 1001-layer ResNet of the original design reaches **7.61%** CIFAR-10 error, against **4.92%** with pre-activation blocks."
  - "**Long training at extreme depth:** the same follow-up reports about **27 hours** on **2 GPUs** for ResNet-1001 on CIFAR, and about **3 weeks** on **8 GPUs** for ResNet-200 on ImageNet."

comparison:
  before: "Plain network"
  after: "Residual network (ResNet)"
  rows:
    - { aspect: "What a few stacked layers learn", before: "The desired mapping H(x) itself", after: "The residual F(x) = H(x) − x; the block outputs F(x) + x", source: "https://arxiv.org/abs/1512.03385" }
    - { aspect: "Cost of the 34-layer network", before: "3.6 billion FLOPs", after: "The same 3.6 billion FLOPs; with zero-padding shortcuts, no extra parameters", source: "https://arxiv.org/abs/1512.03385" }
    - { aspect: "Training error, 34 against 18 layers", before: "The 34-layer network is higher throughout training", after: "The 34-layer network is considerably lower", source: "https://arxiv.org/abs/1512.03385" }
    - { aspect: "ImageNet top-1 validation error, 18 layers (10-crop)", before: "27.94%", after: "27.88%", source: "https://arxiv.org/abs/1512.03385" }
    - { aspect: "ImageNet top-1 validation error, 34 layers (10-crop)", before: "28.54%, worse than at 18 layers", after: "25.03%, better than at 18 layers", source: "https://arxiv.org/abs/1512.03385" }
    - { aspect: "CIFAR-10 at 110 layers", before: "Error above 60% (not plotted in the paper's Figure 6)", after: "6.43% test error", source: "https://arxiv.org/abs/1512.03385" }

key_numbers:
  columns: ["Model", "Building block", "Blocks per stage (conv2 to conv5)", "FLOPs (multiply-adds)", "ImageNet validation error, top-1 / top-5 (10-crop)"]
  rows:
    - { cells: ["VGG-16 (reference)", "No shortcuts: a plain stack of 3×3 convolutions", "not applicable", "15.3 billion", "28.07% / 9.33% (the ResNet authors' test)"], source: "https://arxiv.org/abs/1512.03385" }
    - { cells: ["ResNet-18", "Basic: two 3×3 convolutions", "2, 2, 2, 2", "1.8 billion", "27.88% / top-5 not given"], source: "https://arxiv.org/abs/1512.03385" }
    - { cells: ["ResNet-34", "Basic: two 3×3 convolutions", "3, 4, 6, 3", "3.6 billion", "25.03% / 7.76% (zero-padding shortcuts); 24.52% / 7.46% (projection shortcuts where channels grow)"], source: "https://arxiv.org/abs/1512.03385" }
    - { cells: ["ResNet-50", "Bottleneck: 1×1, 3×3, 1×1 convolutions", "3, 4, 6, 3", "3.8 billion", "22.85% / 6.71%"], source: "https://arxiv.org/abs/1512.03385" }
    - { cells: ["ResNet-101", "Bottleneck: 1×1, 3×3, 1×1 convolutions", "3, 4, 23, 3", "7.6 billion", "21.75% / 6.05%"], source: "https://arxiv.org/abs/1512.03385" }
    - { cells: ["ResNet-152", "Bottleneck: 1×1, 3×3, 1×1 convolutions", "3, 8, 36, 3", "11.3 billion", "21.43% / 5.71%"], source: "https://arxiv.org/abs/1512.03385" }

misunderstandings:
  - misunderstanding: "Deep plain networks did worse because they overfit."
    truth: "The authors show the opposite: the 34-layer plain network has **higher training error** than the 18-layer one throughout training. Overfitting would show low training error and high test error. They call this the **degradation problem** and treat it as an optimisation difficulty; tripling the training iterations did not remove it."
    source: "https://arxiv.org/abs/1512.03385"
  - misunderstanding: "Residual connections were invented to fix vanishing gradients."
    truth: "The authors argue the degradation is **unlikely to be caused by vanishing gradients**: their plain networks use batch normalization, the forward signals have non-zero variance, and they checked that the backward gradients have **healthy norms**. They conjecture instead that deep plain nets may have exponentially low convergence rates. The gradient argument for shortcuts came in the **2016** follow-up."
    source: "https://arxiv.org/abs/1512.03385"
  - misunderstanding: "Every shortcut in a ResNet is a plain copy of the input."
    truth: "Where the number of channels doubles and the map size halves, the input and F have different shapes. The paper either **pads the extra channels with zeros** (option A) or uses a **1 × 1 convolution** as a projection (option B), both with stride 2. ResNet-50, -101 and -152 use option B. Making **all** shortcuts projections (option C) was only marginally better, so the authors did not use it."
    source: "https://arxiv.org/abs/1512.03385"
  - misunderstanding: "A 152-layer ResNet must cost more than the 19-layer VGG, because it is eight times deeper."
    truth: "The authors report **11.3 billion** FLOPs for ResNet-152 against **15.3 billion** for VGG-16 and **19.6 billion** for VGG-19. ResNets use fewer filters per layer, end with global average pooling instead of large fully connected layers, and the bottleneck blocks keep the 3 × 3 convolutions narrow."
    source: "https://arxiv.org/abs/1512.03385"

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
  - { name: "ResNet-50/101/152", year: 2015, note: "Bottleneck residual nets up to 152 layers, 8 times deeper than VGG nets but of lower complexity; an ensemble won 1st place in ILSVRC 2015 classification with 3.57% top-5 test error." }
  - { name: "Wide ResNet", year: 2016, note: "Decreases depth and increases width; the authors report a 16-layer wide residual net outperforming in accuracy and efficiency all earlier deep residual nets, including thousand-layer ones." }
  - { name: "ResNeXt", year: 2016, note: "Each block aggregates a set of transformations with the same topology; the authors report that increasing this 'cardinality' improves accuracy even at the same complexity. 2nd place in ILSVRC 2016 classification." }
  - { name: "DenseNet", year: 2016, note: "Connects each layer to every other layer in a feed-forward fashion, so a block of L layers has L(L+1)/2 direct connections, and joins feature maps by concatenation rather than addition." }

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
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (He, Zhang, Ren & Sun, arXiv:1512.03385, 2015)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1603.05027", title: "Identity Mappings in Deep Residual Networks (He, Zhang, Ren & Sun, arXiv:1603.05027, 2016)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1605.07146", title: "Wide Residual Networks (Zagoruyko & Komodakis, arXiv:1605.07146, 2016)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1611.05431", title: "Aggregated Residual Transformations for Deep Neural Networks (Xie et al., arXiv:1611.05431, 2016)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1608.06993", title: "Densely Connected Convolutional Networks (Huang et al., arXiv:1608.06993, 2016)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1706.03762", title: "Attention Is All You Need (Vaswani et al., arXiv:1706.03762, 2017)", trust: 0.95 }
---

## What Changed

A **plain** network sends the signal through one layer after another, and each few layers must
compute their whole output from scratch. A **residual** network adds a shortcut around every pair
of layers, so those layers only compute a correction that is added to their input.

How to read the table above (it restates Section 4.1 of He et al., 2015):

- **Plain** and **residual** networks in the table have the same layers, depth and width; the only
  difference is the shortcuts.
- **Top-1 error** is the share of validation images whose single best guess is wrong; **top-5**
  counts an image as wrong only if the right class is missing from the five best guesses.
- **10-crop** means each test image is scored on ten cut-out patches and the scores are averaged.
- **FLOPs** here are multiply-adds needed for one image.
- **Training error** going up with depth is the surprise: it rules out overfitting as the cause.

## Key Ideas

- **Residual learning:** a block outputs F(x) + x, so its layers learn only the change F.
- **Degradation, not overfitting:** deeper plain networks had higher training error; residual
  networks of the same size did not.
- **Free shortcuts:** an identity shortcut has no weights and adds only one element-wise addition.
- **Bottlenecks for depth:** 1 × 1 convolutions narrow and widen the channels around a 3 × 3
  convolution, which keeps 50- to 152-layer networks affordable.

## How It Works

The figure above has two parts. **Left:** one residual block, read from top to bottom. **Right:**
how **ResNet-34** stacks 16 such blocks in four stages.

A **feature map** is the grid of numbers a layer outputs, one grid per **channel**. A **3 × 3
convolution** slides a small grid of learned weights over the map (see the CNN page). **Batch
normalization** rescales each channel using statistics of the current mini-batch of images. **ReLU**
sets negative values to 0.

1. **Input x.** The block receives the feature maps from the block below; in the first stage of
   ResNet-34 that is **64** channels of **56 × 56**.
2. **First weight layer.** A 3 × 3 convolution, then batch normalization, then a ReLU. The paper
   puts batch normalization right after each convolution and before the activation.
3. **Second weight layer.** Another 3 × 3 convolution and batch normalization, **no ReLU yet**.
   Its output is **F(x)**, the residual.
4. **Identity shortcut (the accent line).** The input x goes around both layers unchanged. It has
   **no weights** and costs nothing to compute.
5. **Add (the + circle).** F(x) and x are added element by element, channel by channel.
6. **ReLU after the addition.** The block's output is y = ReLU(F(x) + x), and it becomes the input
   of the next block.
7. **Stem (top of the right panel).** The **224 × 224** image goes through a **7 × 7** convolution
   with 64 filters and stride 2 (112 × 112), then **3 × 3 max pooling** with stride 2 (56 × 56).
8. **Four stages.** conv2_x has **3** blocks at 64 channels (56 × 56), conv3_x **4** blocks at 128
   (28 × 28), conv4_x **6** blocks at 256 (14 × 14) and conv5_x **3** blocks at 512 (7 × 7).
9. **Changing stages (dashed shortcuts).** The first block of conv3_x, conv4_x and conv5_x uses
   **stride 2**: the map size halves and the channels double. Its shortcut pads the new channels
   with zeros or uses a 1 × 1 projection, also with stride 2.
10. **Head.** **Global average pooling** turns each 7 × 7 map into one number, then a **1000-way**
    fully connected layer and softmax give one probability per ImageNet class. The count is
    1 + 2 × 16 + 1 = **34** layers with weights.
11. **Deeper versions.** ResNet-50, -101 and -152 use a **bottleneck** block instead: a 1 × 1
    convolution narrows 256 channels to 64, a 3 × 3 convolution works on 64, and a 1 × 1
    convolution widens back to 256, all around the same identity shortcut.

## Worked Example

**One residual block by hand**, then what the shortcut does when a block should change nothing,
and what it does to gradients. The numbers are made up and small so every step can be checked: an
example, not from a trained model.

As in the paper's own equation, biases and batch normalization are left out, and each "layer"
multiplies a vector of **two** numbers by a 2 × 2 grid of weights (a real block convolves feature
maps with 3 × 3 filters).

| Part | First number (or row) | Second number (or row) |
| --- | --- | --- |
| Input x | 1 | 2 |
| First layer W₁ | (1, −1) | (0, 1) |
| Second layer W₂ | (0.5, 0) | (0, −0.25) |

1. **First layer, then ReLU.** Multiply each row of W₁ by x and add:
   - row 1: 1 × 1 + (−1) × 2 = **−1**
   - row 2: 0 × 1 + 1 × 2 = **2**
   - ReLU sets the negative value to 0: **(0, 2)**.
2. **Second layer.** Multiply each row of W₂ by (0, 2):
   - row 1: 0.5 × 0 + 0 × 2 = **0**
   - row 2: 0 × 0 + (−0.25) × 2 = **−0.5**
   - This is the residual: F(x) = **(0, −0.5)**.
3. **Add the shortcut, then ReLU.** (0, −0.5) + (1, 2) = (1, 1.5). Both values are positive, so
   ReLU leaves them: y = **(1, 1.5)**.
4. **The same layers without the shortcut.** A plain block outputs ReLU(F(x)) = ReLU(0, −0.5) =
   **(0, 0)**: the input is lost. The residual block passed the input on with a small change.
5. **A block that should change nothing.** Set every weight to 0:
   - F(x) = **(0, 0)**, so y = ReLU((0, 0) + (1, 2)) = **(1, 2)**, exactly the input.
   - A plain block with zero weights outputs **(0, 0)**. To pass (1, 2) on, it would need both
     layers to be identity grids, rows (1, 0) and (0, 1); the authors suggest that solvers have
     difficulty finding such identity mappings with several nonlinear layers.
6. **A gradient through 10 blocks.** Simplify further, as He et al. (2016) do in their analysis:
   one number per block, no ReLU and no batch normalization, every weight **w = 0.1**.
   - A plain block multiplies its input by w, so its derivative is **0.1**.
   - A residual block computes x + w·x, so its derivative is 1 + 0.1 = **1.1**.
   - Through 10 plain blocks: 0.1 to the power 10 = **0.0000000001**.
   - Through 10 residual blocks: 1.1 to the power 10 ≈ **2.594**.
   - The "1 +" comes from the shortcut. It is why, in He et al.'s words, the gradient does not
     vanish even when the weights are arbitrarily small.
7. **What the blocks cost in weights.** Bias and batch normalization numbers left out:
   - Basic block on 64 channels: 3 × 3 × 64 × 64 = 36,864 per layer, so **73,728** for two layers.
   - Bottleneck block on 256 channels: 256 × 64 = 16,384, then 3 × 3 × 64 × 64 = 36,864, then
     64 × 256 = 16,384, so **69,632** in total. The paper says the two designs have similar time
     complexity.
   - The identity shortcut adds **0**. A 1 × 1 projection from 256 to 256 channels would add
     256 × 256 = 65,536, giving 135,168, about **1.94 times** as many, the doubling the authors
     warn about.

**What this means:** the shortcut makes "change nothing" the easy default (all weights near zero),
keeps the input alive when the layers' output is small or negative, and gives gradients a path
that does not shrink with depth. It does this with no extra weights, which is how ResNet-152 stays
below VGG-19's cost.
