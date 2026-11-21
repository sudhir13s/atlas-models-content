---
id: architecture-era/cnn
type: architecture-era
slug: cnn
schema_version: 1.0
title: "Convolutional Neural Networks"
name: "Convolutional Neural Network"
era: foundation
era_range: "1989–2015"
year: 2012
family: vision
core_innovation: "Weight-shared convolutional filters plus pooling exploit spatial locality, cutting parameters and building translation-tolerant features."
description: "From LeNet to AlexNet — the architecture that made GPU-trained deep vision work at scale."
key_features: ["Weight-shared convolution kernels", "Local receptive fields", "Pooling for translation tolerance", "Hierarchical feature learning", "GPU-scale training"]
key_models: ["LeNet-5 (1998)", "AlexNet (2012)", "VGG (2014)", "GoogLeNet (2014)"]
paper_title: "ImageNet Classification with Deep Convolutional Neural Networks (AlexNet)"
paper_url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
influenced_by: [architecture-era/mlp-backprop]
led_to: [architecture-era/resnet, architecture-era/vision-transformer]

eli5: "A convolutional neural network looks at a picture through a tiny sliding window, hunting for one simple pattern — an edge, a corner, a patch of colour. It slides that same window over the whole image, so it can spot the pattern anywhere. Stack these searches and simple patterns combine into edges, then shapes, then whole objects like a cat or a car."

analogy:
  title: "A rubber stamp that hunts for one pattern"
  body: "Imagine a small rubber stamp cut to detect one thing — say, a vertical edge. You press it at the top-left of a photo: bright ink where an edge is, faint where none. Then you slide it one step right and stamp again, marching across the whole image. The sheet of stamps you produce is a map of where that edge appears — a 'feature map'. Crucially it is the *same* stamp everywhere, so an edge in the corner and an edge in the centre light up identically. A convolutional network is a stack of thousands of learned stamps: the first layer stamps for edges and colours, and later layers stamp for combinations of the earlier maps — eyes, wheels, faces."

narrative:
  motivation: "By the late 1980s neural networks could classify small images, but only as flat vectors: every pixel wired to every hidden unit. A modest 200×200 image already needs tens of thousands of weights per neuron, so parameters exploded, training overfit, and — worst of all — the network had to relearn the same pattern separately in every location. A cat in the top-left and a cat in the bottom-right looked like two unrelated problems."
  approach: "The insight, from LeCun's work on handwritten digits, was to bake the geometry of images into the architecture. Vision has two facts a dense network ignores: patterns are *local* (a pixel relates most strongly to its neighbours) and *position-independent* (an edge is an edge wherever it sits). So use a small filter that looks at a local patch, and slide that *same* filter across the whole image."
  solution: "This gives three ingredients. Convolution: a small kernel of shared weights sweeps the image, producing a feature map of where its pattern occurs. Nonlinearity (ReLU): keep the strong responses, zero the rest. Pooling: downsample each map so the network becomes tolerant to small shifts and later layers see a wider field of view. Stack these blocks and features compose — edges into textures, textures into parts, parts into objects."
  outcome: "LeNet-5 proved the idea on cheques and ZIP codes in the 1990s, but compute held it back. In 2012 AlexNet — a deep convolutional network with ReLU, dropout, data augmentation, and two GPUs — nearly halved the error rate on the ImageNet benchmark overnight. That single result ended the era of hand-designed image features and launched modern deep learning. Every later vision model, from ResNet to the Vision Transformer, is a descendant of this moment."

build_steps:
  - { title: "1. Start with the raw pixels", detail: "An image is a grid of numbers: height × width × 3 colour channels (red, green, blue). This grid, not a flattened vector, is what the network reads — the spatial layout is kept intact." }
  - { title: "2. Convolve with learned filters", detail: "Slide each small filter (e.g. 3×3) across the image, computing a weighted sum of the patch under it at every position. Each filter outputs one feature map; a layer has many filters, so it outputs a stack of maps." }
  - { title: "3. Apply a nonlinearity (ReLU)", detail: "Pass every value through ReLU — keep it if positive, set it to zero if negative. Without this the whole stack would collapse into one linear filter and could not learn curved, complex patterns." }
  - { title: "4. Pool to shrink and stabilise", detail: "Downsample each map (e.g. take the maximum in each 2×2 block). This halves the resolution, makes the features tolerant to small shifts, and lets deeper layers cover more of the image." }
  - { title: "5. Stack blocks to build a hierarchy", detail: "Repeat convolve → ReLU → pool many times. Early layers detect edges and colour blobs; middle layers detect textures and parts; deep layers respond to whole objects. Channels grow as spatial size shrinks." }
  - { title: "6. Flatten and classify", detail: "After the final block, flatten the small, deep feature maps into a vector and feed it to a few fully connected layers plus a softmax that outputs a probability for each class." }

key_equation:
  latex: "S(i,j)=\\sum_{m}\\sum_{n} I(i+m,\\, j+n)\\,K(m,n)"
  plain: "To get the feature-map value at position (i, j), lay the small filter K over the patch of the image starting at (i, j), multiply each filter weight by the pixel it covers, and add them all up. Slide the filter to the next position and repeat. Because the same K is used everywhere, the network detects its pattern wherever it appears."
  terms:
    - { symbol: "S(i,j)", meaning: "the output feature map — how strongly the filter's pattern is present at position (i, j)" }
    - { symbol: "I", meaning: "the input image (or the previous layer's feature map)" }
    - { symbol: "K(m,n)", meaning: "the learned filter/kernel of shared weights, indexed over its small window" }
    - { symbol: "m, n", meaning: "offsets that sweep across the filter's window (e.g. 0–2 for a 3×3 kernel)" }

strengths:
  - "Weight sharing means a pattern is learned once and detected everywhere, so far fewer parameters than a dense network on the same image."
  - "Built-in translation tolerance — an object is recognised no matter where it sits in the frame."
  - "Learns a natural hierarchy: edges → textures → parts → objects, with no hand-engineered features."
  - "Strong results even on modest datasets, because the spatial prior does a lot of the work."

weaknesses:
  - "The receptive field grows slowly with depth, so capturing very long-range or global relationships needs many layers."
  - "Baked-in locality is a hard prior — helpful on images, but limiting where relationships are not spatial."
  - "Pooling and strided convolution discard precise spatial detail, which hurts tasks needing exact localisation."
  - "Fixed grid structure makes it awkward for irregular data (graphs, point clouds, variable-length sequences)."

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 900 440" role="img" aria-label="LeNet-5: a 32 by 32 input passes through convolution and subsampling layers whose feature maps shrink from 28 by 28 to 5 by 5 while growing from 6 to 16 maps, then through 120, 84 and 10 units; a 5 by 5 window on the input feeds one unit of the first feature map.">
  <defs><marker id="cnn-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="cnn-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <path class="d-line-strong" d="M133.2 200 H179.2" marker-end="url(#cnn-arrow)"/>
  <text class="d-label" x="156.2" y="318" text-anchor="middle">convolution 5×5</text>
  <text class="d-note" x="156.2" y="332" text-anchor="middle">shared weights</text>
  <path class="d-line" stroke-dasharray="2 3" d="M156.2 210 V304"/>
  <path class="d-line-strong" d="M308 200 H344" marker-end="url(#cnn-arrow)"/>
  <text class="d-label" x="326" y="318" text-anchor="middle">subsampling 2×2</text>
  <text class="d-note" x="326" y="332" text-anchor="middle">average, ×coef + bias</text>
  <path class="d-line" stroke-dasharray="2 3" d="M326 210 V304"/>
  <path class="d-line-strong" d="M424.4 200 H460.4" marker-end="url(#cnn-arrow)"/>
  <text class="d-label" x="442.4" y="318" text-anchor="middle">convolution 5×5</text>
  <text class="d-note" x="442.4" y="332" text-anchor="middle">sparse map links</text>
  <path class="d-line" stroke-dasharray="2 3" d="M442.4 210 V304"/>
  <path class="d-line-strong" d="M548.4 200 H584.4" marker-end="url(#cnn-arrow)"/>
  <text class="d-label" x="566.4" y="318" text-anchor="middle">subsampling 2×2</text>
  <text class="d-note" x="566.4" y="332" text-anchor="middle">average, ×coef + bias</text>
  <path class="d-line" stroke-dasharray="2 3" d="M566.4 210 V304"/>
  <path class="d-line" d="M650.9 169.8 L712.9 134 M650.9 230.3 L712.9 266"/>
  <path class="d-line-strong" d="M656.9 200 H706.9" marker-end="url(#cnn-arrow)"/>
  <text class="d-label" x="681.9" y="318" text-anchor="middle">convolution 5×5</text>
  <text class="d-note" x="681.9" y="332" text-anchor="middle">5×5 → 1×1</text>
  <path class="d-line" stroke-dasharray="2 3" d="M681.9 210 V304"/>
  <path class="d-line" d="M724.9 134 L786.9 154 M724.9 266 L786.9 246"/>
  <path class="d-line-strong" d="M730.9 200 H780.9" marker-end="url(#cnn-arrow)"/>
  <text class="d-label" x="755.9" y="318" text-anchor="middle">full</text>
  <text class="d-note" x="755.9" y="332" text-anchor="middle">connection</text>
  <path class="d-line" stroke-dasharray="2 3" d="M755.9 210 V304"/>
  <path class="d-line" d="M798.9 154 L858.9 136.5 M798.9 246 L858.9 263.5"/>
  <path class="d-line-strong" d="M804.9 200 H852.9" marker-end="url(#cnn-arrow)"/>
  <text class="d-label" x="828.9" y="318" text-anchor="middle">full connection</text>
  <text class="d-note" x="828.9" y="332" text-anchor="middle">10 RBF units</text>
  <path class="d-line" stroke-dasharray="2 3" d="M828.9 210 V304"/>
  <rect class="d-node" x="28" y="150.4" width="99.2" height="99.2"/>
  <rect class="d-box d-role-input" x="28" y="150.4" width="99.2" height="99.2"/>
  <rect class="d-node" x="215.2" y="141.6" width="86.8" height="86.8"/>
  <rect class="d-box d-role-vision" x="215.2" y="141.6" width="86.8" height="86.8"/>
  <rect class="d-node" x="209.2" y="147.6" width="86.8" height="86.8"/>
  <rect class="d-box d-role-vision" x="209.2" y="147.6" width="86.8" height="86.8"/>
  <rect class="d-node" x="203.2" y="153.6" width="86.8" height="86.8"/>
  <rect class="d-box d-role-vision" x="203.2" y="153.6" width="86.8" height="86.8"/>
  <rect class="d-node" x="197.2" y="159.6" width="86.8" height="86.8"/>
  <rect class="d-box d-role-vision" x="197.2" y="159.6" width="86.8" height="86.8"/>
  <rect class="d-node" x="191.2" y="165.6" width="86.8" height="86.8"/>
  <rect class="d-box d-role-vision" x="191.2" y="165.6" width="86.8" height="86.8"/>
  <rect class="d-node" x="185.2" y="171.6" width="86.8" height="86.8"/>
  <rect class="d-box d-role-vision" x="185.2" y="171.6" width="86.8" height="86.8"/>
  <rect class="d-node" x="375" y="165.8" width="43.4" height="43.4"/>
  <rect class="d-box d-role-router" x="375" y="165.8" width="43.4" height="43.4"/>
  <rect class="d-node" x="370" y="170.8" width="43.4" height="43.4"/>
  <rect class="d-box d-role-router" x="370" y="170.8" width="43.4" height="43.4"/>
  <rect class="d-node" x="365" y="175.8" width="43.4" height="43.4"/>
  <rect class="d-box d-role-router" x="365" y="175.8" width="43.4" height="43.4"/>
  <rect class="d-node" x="360" y="180.8" width="43.4" height="43.4"/>
  <rect class="d-box d-role-router" x="360" y="180.8" width="43.4" height="43.4"/>
  <rect class="d-node" x="355" y="185.8" width="43.4" height="43.4"/>
  <rect class="d-box d-role-router" x="355" y="185.8" width="43.4" height="43.4"/>
  <rect class="d-node" x="350" y="190.8" width="43.4" height="43.4"/>
  <rect class="d-box d-role-router" x="350" y="190.8" width="43.4" height="43.4"/>
  <rect class="d-node" x="511.4" y="162" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="511.4" y="162" width="31" height="31"/>
  <rect class="d-node" x="508.4" y="165" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="508.4" y="165" width="31" height="31"/>
  <rect class="d-node" x="505.4" y="168" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="505.4" y="168" width="31" height="31"/>
  <rect class="d-node" x="502.4" y="171" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="502.4" y="171" width="31" height="31"/>
  <rect class="d-node" x="499.4" y="174" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="499.4" y="174" width="31" height="31"/>
  <rect class="d-node" x="496.4" y="177" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="496.4" y="177" width="31" height="31"/>
  <rect class="d-node" x="493.4" y="180" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="493.4" y="180" width="31" height="31"/>
  <rect class="d-node" x="490.4" y="183" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="490.4" y="183" width="31" height="31"/>
  <rect class="d-node" x="487.4" y="186" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="487.4" y="186" width="31" height="31"/>
  <rect class="d-node" x="484.4" y="189" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="484.4" y="189" width="31" height="31"/>
  <rect class="d-node" x="481.4" y="192" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="481.4" y="192" width="31" height="31"/>
  <rect class="d-node" x="478.4" y="195" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="478.4" y="195" width="31" height="31"/>
  <rect class="d-node" x="475.4" y="198" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="475.4" y="198" width="31" height="31"/>
  <rect class="d-node" x="472.4" y="201" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="472.4" y="201" width="31" height="31"/>
  <rect class="d-node" x="469.4" y="204" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="469.4" y="204" width="31" height="31"/>
  <rect class="d-node" x="466.4" y="207" width="31" height="31"/>
  <rect class="d-box d-role-vision" x="466.4" y="207" width="31" height="31"/>
  <rect class="d-node" x="635.4" y="169.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="635.4" y="169.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="632.4" y="172.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="632.4" y="172.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="629.4" y="175.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="629.4" y="175.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="626.4" y="178.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="626.4" y="178.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="623.4" y="181.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="623.4" y="181.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="620.4" y="184.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="620.4" y="184.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="617.4" y="187.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="617.4" y="187.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="614.4" y="190.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="614.4" y="190.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="611.4" y="193.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="611.4" y="193.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="608.4" y="196.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="608.4" y="196.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="605.4" y="199.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="605.4" y="199.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="602.4" y="202.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="602.4" y="202.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="599.4" y="205.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="599.4" y="205.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="596.4" y="208.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="596.4" y="208.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="593.4" y="211.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="593.4" y="211.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="590.4" y="214.8" width="15.5" height="15.5"/>
  <rect class="d-box d-role-router" x="590.4" y="214.8" width="15.5" height="15.5"/>
  <rect class="d-node" x="712.9" y="134" width="12" height="132"/>
  <rect class="d-box d-role-vision" x="712.9" y="134" width="12" height="132"/>
  <rect class="d-node" x="786.9" y="154" width="12" height="92"/>
  <rect class="d-box d-role-feedforward" x="786.9" y="154" width="12" height="92"/>
  <circle class="d-node d-role-output" cx="863.9" cy="141.5" r="5"/>
  <circle class="d-node d-role-output" cx="863.9" cy="154.5" r="5"/>
  <circle class="d-node d-role-output" cx="863.9" cy="167.5" r="5"/>
  <circle class="d-node d-role-output" cx="863.9" cy="180.5" r="5"/>
  <circle class="d-node d-role-output" cx="863.9" cy="193.5" r="5"/>
  <circle class="d-node d-role-output" cx="863.9" cy="206.5" r="5"/>
  <circle class="d-node d-role-output" cx="863.9" cy="219.5" r="5"/>
  <circle class="d-node d-role-output" cx="863.9" cy="232.5" r="5"/>
  <circle class="d-node d-role-output" cx="863.9" cy="245.5" r="5"/>
  <circle class="d-node d-role-output" cx="863.9" cy="258.5" r="5"/>
  <text class="d-title" x="77.6" y="52" text-anchor="middle">Input</text>
  <text class="d-note" x="77.6" y="67" text-anchor="middle">32×32</text>
  <text class="d-title" x="243.6" y="52" text-anchor="middle">C1</text>
  <text class="d-note" x="243.6" y="67" text-anchor="middle">6 @ 28×28</text>
  <text class="d-title" x="384.2" y="52" text-anchor="middle">S2</text>
  <text class="d-note" x="384.2" y="67" text-anchor="middle">6 @ 14×14</text>
  <text class="d-title" x="504.4" y="52" text-anchor="middle">C3</text>
  <text class="d-note" x="504.4" y="67" text-anchor="middle">16 @ 10×10</text>
  <text class="d-title" x="620.6" y="52" text-anchor="middle">S4</text>
  <text class="d-note" x="620.6" y="67" text-anchor="middle">16 @ 5×5</text>
  <text class="d-title" x="718.9" y="52" text-anchor="middle">C5</text>
  <text class="d-note" x="718.9" y="67" text-anchor="middle">120</text>
  <text class="d-title" x="792.9" y="52" text-anchor="middle">F6</text>
  <text class="d-note" x="792.9" y="67" text-anchor="middle">84</text>
  <text class="d-title" x="863.9" y="52" text-anchor="middle">Output</text>
  <text class="d-note" x="863.9" y="67" text-anchor="middle">10</text>
  <rect class="d-box d-role-attention" x="59" y="169" width="15.5" height="15.5" stroke-dasharray="2 2"/>
  <rect class="d-box d-role-attention" x="55.9" y="169" width="15.5" height="15.5"/>
  <path class="d-line-accent" d="M71.4 169 L213.1 190.2 M71.4 184.5 L213.1 193.3"/>
  <rect class="d-box d-role-attention" x="213.1" y="190.2" width="4.34" height="4.34"/>
  <path class="d-line-accent" d="M55.9 194.5 H85.4" marker-end="url(#cnn-arrow-accent)"/>
  <text class="d-note" x="77.6" y="267.6" text-anchor="middle">5×5 window slides</text>
  <text class="d-note" x="77.6" y="280.6" text-anchor="middle">one step at a time</text>
  <text class="d-note" x="228.6" y="276.4" text-anchor="middle">each window gives</text>
  <text class="d-note" x="228.6" y="289.4" text-anchor="middle">one unit of a map</text>
  <path class="d-line" d="M28 362 V368 H650.9 V362"/>
  <text class="d-label" x="339.4" y="384" text-anchor="middle">feature extraction: maps shrink in size, grow in number</text>
  <path class="d-line" d="M712.9 362 V368 H868.9 V362"/>
  <text class="d-label" x="790.9" y="384" text-anchor="middle">classifier</text>
  <text class="d-note" x="450" y="428" text-anchor="middle">LeNet-5 (LeCun et al., 1998): tanh-type squashing after every layer; each output unit scores its digit by distance from F6 (Euclidean RBF)</text>
  </svg>
diagram_caption: "LeNet-5: a 5×5 window slides over the 32×32 input, each position giving one unit of a feature map; convolution and 2×2 subsampling shrink the maps (28→14→10→5) while their number grows (6→16), then 120, 84 and 10 units classify the digit."
notable_models:
  - { name: "LeNet-5", year: 1998, note: "Proved convolution + pooling could read handwritten digits reliably — the blueprint for every CNN that followed." }
  - { name: "AlexNet", year: 2012, note: "Showed a deep CNN with ReLU, dropout, and GPU training could crush the ImageNet benchmark — the spark of the deep learning boom." }
  - { name: "VGG", year: 2014, note: "Proved that stacking many small 3×3 filters into a deep, uniform network keeps improving accuracy." }
  - { name: "GoogLeNet / Inception", year: 2014, note: "Showed multi-scale filters in parallel (the Inception block) give accuracy at a fraction of the compute." }

resources:
  - { kind: course, title: "CS231n: Convolutional Neural Networks for Visual Recognition (Stanford)", url: "https://cs231n.github.io/convolutional-networks/" }
  - { kind: blog, title: "Conv Nets: A Modular Perspective (Chris Olah)", url: "https://colah.github.io/posts/2014-07-Conv-Nets-Modular/" }
  - { kind: video, title: "But what is a convolution? (3Blue1Brown)", url: "https://www.youtube.com/watch?v=KuXjwB4LzSA" }
  - { kind: paper, title: "ImageNet Classification with Deep CNNs / AlexNet (Krizhevsky et al., 2012)", url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html" }
  - { kind: paper, title: "Gradient-Based Learning Applied to Document Recognition / LeNet (LeCun et al., 1998)", url: "https://doi.org/10.1109/5.726791" }

learn_first:
  - { label: "The multilayer perceptron & backprop", href: "/models/architecture/mlp-backprop", tag: "Architecture" }
  - { label: "What a neural network is", href: "/learn/concepts/neural-network", tag: "Concept" }
  - { label: "ReLU & activation functions", href: "/learn/concepts/activation-functions", tag: "Math" }

tags: [cnn, vision, convolution, alexnet, foundational]
updated: 2026-07-20
sources:
  - { url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html", title: "ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)", trust: 0.95 }
  - { url: "https://doi.org/10.1109/5.726791", title: "Gradient-Based Learning Applied to Document Recognition (LeCun et al., 1998)", trust: 0.9 }
  - { url: "https://cs231n.github.io/convolutional-networks/", title: "CS231n Convolutional Networks (Stanford)", trust: 0.9 }
---

## What Changed
A fully connected network treats an image as an unstructured vector: every pixel connects to every
hidden unit, so parameters explode and the model must relearn a pattern separately in every location.
The convolutional network bakes in the geometry of images. A small kernel slides across the image,
sharing the *same* weights at every position — this is a strong prior (features are useful wherever
they appear) that slashes parameter count and gives translation tolerance. Stacking convolution and
pooling builds a hierarchy: edges → textures → parts → objects. LeCun's LeNet proved the idea on
digits in the 1990s, but it was AlexNet in 2012 — deep, ReLU-activated, dropout-regularised, and
trained on two graphics processing units (GPUs) — that halved the ImageNet error rate overnight and
triggered the modern deep learning boom. It descends directly from the backprop-trained multilayer
perceptron and set up both very deep residual nets and, eventually, the patch-based Vision Transformer.

## Key Ideas
- **Weight sharing** — one kernel is reused across all spatial positions, so the model learns a
  feature once and detects it everywhere, with far fewer parameters than a dense layer.
- **Local receptive fields** — each unit sees a small neighbourhood; depth grows the effective
  receptive field so deeper layers integrate wider context.
- **Pooling** — downsampling (max/average) adds translation tolerance and shrinks spatial resolution
  while growing channel depth.
- **Hierarchical features** — early layers learn edges and colour blobs, later layers learn object
  parts, an emergent compositional representation.
- **Scale + hardware** — ReLU, dropout, data augmentation, and GPU training (AlexNet) were the
  practical unlocks that turned a decades-old idea into a breakthrough.
