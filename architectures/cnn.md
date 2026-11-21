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
description: "Convolutional networks from LeNet to AlexNet, a deep convolutional network trained on two GPUs for ImageNet classification."
key_features: ["Weight-shared convolution kernels", "Local receptive fields", "Pooling (subsampling) layers", "Stacked convolutional layers"]
key_models: ["LeNet-5 (1998)", "AlexNet (2012)", "VGG (2014)", "GoogLeNet (2014)"]
paper_title: "ImageNet Classification with Deep Convolutional Neural Networks (AlexNet)"
paper_url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
influenced_by: [architecture-era/mlp-backprop]
led_to: [architecture-era/resnet, architecture-era/vision-transformer]

eli5: "A convolutional neural network looks at a picture through a tiny sliding window, hunting for one simple pattern — an edge, a corner, a patch of colour. It slides that same window over the whole image, so it can spot the pattern anywhere. Stack these searches and later layers combine the simple patterns found by earlier ones."

analogy:
  title: "A rubber stamp that hunts for one pattern"
  body: "Imagine a small rubber stamp cut to detect one thing — say, a vertical edge. You press it at the top-left of a photo: bright ink where an edge is, faint where none. Then you slide it one step right and stamp again, marching across the whole image. The sheet of stamps you produce is a map of where that edge appears — a 'feature map'. Crucially it is the *same* stamp everywhere, so an edge in the corner and an edge in the centre light up identically. A convolutional network is a stack of many learned stamps: the first layer stamps the image, and later layers stamp for combinations of the earlier maps."

narrative:
  motivation: "A fully connected network reads an image as a flat vector, with every pixel wired to every hidden unit. A 200×200 image needs 40,000 weights per hidden unit, and the network must learn the same pattern separately in every location."
  approach: "LeCun and colleagues applied backpropagation to convolutional networks for handwritten zip codes (1989) and document recognition (1998). The architecture builds in two facts a dense network ignores: patterns are *local*, and the same pattern can appear anywhere. So a small filter looks at a local patch, and the *same* filter slides across the whole image."
  solution: "Convolution: a small kernel of shared weights sweeps the image, producing a feature map of where its pattern occurs. Nonlinearity: for example ReLU in AlexNet. Pooling: downsample each map so later layers see a wider field of view. Stacked layers combine the feature maps of earlier layers."
  outcome: "In 2012 AlexNet, a deep convolutional network with ReLU, dropout, data augmentation and two GPUs, won the ILSVRC-2012 competition with a top-5 test error of 15.3%, against 26.2% for the second-best entry (Krizhevsky, Sutskever and Hinton)."

build_steps:
  - { title: "1. Start with the raw pixels", detail: "An image is a grid of numbers: height × width × 3 colour channels (red, green, blue). This grid, not a flattened vector, is what the network reads — the spatial layout is kept intact." }
  - { title: "2. Convolve with learned filters", detail: "Slide each small filter (e.g. 3×3) across the image, computing a weighted sum of the patch under it at every position. Each filter outputs one feature map; a layer has many filters, so it outputs a stack of maps." }
  - { title: "3. Apply a nonlinearity", detail: "Pass every value through a non-linear function, such as ReLU in AlexNet (keep it if positive, set it to zero if negative). Without this the whole stack would collapse into one linear filter." }
  - { title: "4. Pool to shrink", detail: "Downsample each map (e.g. average or take the maximum in each 2×2 block). This halves the resolution and lets deeper layers cover more of the image." }
  - { title: "5. Stack blocks", detail: "Repeat convolve → nonlinearity → pool several times. Each layer combines the feature maps of the layer below." }
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
  - "The same filter responds to its pattern wherever it sits in the frame."
  - "Features are learned from data rather than hand-engineered."

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
  - { name: "LeNet-5", year: 1998, note: "LeCun, Bottou, Bengio and Haffner's convolutional network for document recognition (Proceedings of the IEEE)." }
  - { name: "AlexNet", year: 2012, note: "Won ILSVRC-2012 with a 15.3% top-5 test error against 26.2% for the second-best entry, using ReLU, dropout and two GPUs." }
  - { name: "VGG", year: 2014, note: "The authors report a significant improvement by pushing depth to 16–19 weight layers with very small (3×3) filters." }
  - { name: "GoogLeNet / Inception", year: 2014, note: "A 22-layer network whose Inception design increases depth and width while keeping the computational budget constant." }

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
updated: '2026-11-21'
sources:
  - { url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html", title: "ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)", trust: 0.95 }
  - { url: "https://doi.org/10.1109/5.726791", title: "Gradient-Based Learning Applied to Document Recognition (LeCun et al., 1998)", trust: 0.9 }
  - { url: "https://doi.org/10.1162/neco.1989.1.4.541", title: "Backpropagation Applied to Handwritten Zip Code Recognition (LeCun et al., 1989, Neural Computation)", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks for Large-Scale Image Recognition (Simonyan & Zisserman, arXiv:1409.1556)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.4842", title: "Going Deeper with Convolutions (Szegedy et al., arXiv:1409.4842)", trust: 0.95 }
  - { url: "https://cs231n.github.io/convolutional-networks/", title: "CS231n Convolutional Networks (Stanford)", trust: 0.9 }
---

## What Changed
A fully connected network treats an image as an unstructured vector: every pixel connects to every
hidden unit, and the model must learn a pattern separately in every location.

The convolutional network slides a small kernel across the image, sharing the *same* weights at every
position. This cuts the parameter count, and stacking convolution and pooling layers lets later layers
combine the feature maps of earlier ones.

LeCun and colleagues trained convolutional networks with backpropagation on handwritten zip codes
(1989) and documents (1998). In 2012 AlexNet, trained on two graphics processing units (GPUs) with ReLU,
dropout and data augmentation, won ILSVRC-2012 with a 15.3% top-5 test error against 26.2% for the
second-best entry.

## Key Ideas
- **Weight sharing** — one kernel is reused across all spatial positions, so the model learns a
  feature once and detects it everywhere, with far fewer parameters than a dense layer.
- **Local receptive fields** — each unit sees a small neighbourhood; depth grows the effective
  receptive field so deeper layers integrate wider context.
- **Pooling** — downsampling (max or average) shrinks spatial resolution.
- **Stacked layers** — each convolutional layer takes the feature maps of the layer below as input.
- **AlexNet's training choices** — ReLU, dropout, data augmentation and training split across two
  GPUs.
