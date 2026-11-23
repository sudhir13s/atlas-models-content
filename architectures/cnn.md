---
id: architecture-era/cnn
type: architecture-era
slug: cnn
schema_version: 2.7
title: "Convolutional Neural Networks"
name: "Convolutional Neural Network"
era: foundation
era_range: "1989–2015"
year: 2012
family: vision
core_innovation: "Weight-shared convolutional filters plus pooling exploit spatial locality, cutting parameters and building translation-tolerant features."
description: "A neural network for images that slides small filters of shared weights across the picture, so each filter finds its pattern wherever it appears, and stacks such layers so that later ones combine the patterns earlier ones found."
key_features: ["Weight-shared convolution kernels", "Local receptive fields", "Pooling (subsampling) layers", "Stacked convolutional layers"]
key_models: ["LeNet-5 (1998)", "AlexNet (2012)", "VGG (2014)", "GoogLeNet (2014)"]
paper_title: "ImageNet Classification with Deep Convolutional Neural Networks (AlexNet)"
paper_url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
influenced_by: [architecture-era/mlp-backprop]
led_to: [architecture-era/resnet, architecture-era/vision-transformer]

analogy:
  title: "A rubber stamp that hunts for one pattern"
  body: "This is an analogy, not the mechanism. Picture a small rubber stamp cut to respond to one thing, say a dark-to-bright vertical edge. You press it on the top-left corner of a photo and write down how strongly it matched, then move it one step and press again, until you have covered the whole photo. The sheet of numbers you wrote is a feature map: a map of where that edge appears. It is the same stamp everywhere, so an edge in a corner and an edge in the centre score alike. A convolutional layer holds many stamps whose patterns are learned, and the next layer stamps the maps of the layer below. The exact rule is the key equation below."

narrative:
  motivation: "**A fully connected network wastes weights on images and ignores their layout.** The case is set out by **LeCun, Bottou, Bengio and Haffner (1998)**.\n\n- **Too many weights:** a typical image has several hundred pixels, so a first layer of **100 hidden units** connected to every pixel already has **several tens of thousands** of weights.\n- **No built-in shift tolerance:** such a network has no built-in invariance to **translations or local distortions**; it would have to learn the same detector again at every position.\n- **Layout ignored:** the pixels could be fed in **any fixed order** without changing training, although neighbouring pixels are highly correlated."
  approach: "**Three ideas built into the architecture.** LeCun et al. (1998) name them:\n\n- **Local receptive fields:** each unit sees only a small neighbourhood of the layer below, so it can detect edges, end-points and corners.\n- **Shared weights:** all units of one feature map use the **same weights**, so a detector learned in one place works everywhere.\n- **Sub-sampling:** lower the resolution of each map so the exact position of a feature matters less.\n- **Roots:** the authors trace local receptive fields to the **Perceptron** of the early 1960s and **Hubel and Wiesel**, and the convolution and sub-sampling pair to **Fukushima's Neocognitron**, which had no supervised training procedure such as backpropagation.\n- **First trained by backpropagation:** **LeCun et al. (1989)** fed images of US Postal Service zip code digits **directly** to such a network, rather than feature vectors."
  solution: "**LeNet-5: convolution and sub-sampling, alternated, then a classifier.**\n\n- **Layers:** **7** layers with weights, reading a **32 × 32** pixel image.\n- **Feature maps:** **6** maps of 28 × 28, then 6 of 14 × 14, **16** of 10 × 10, 16 of 5 × 5, then 120 and 84 units and **10** outputs.\n- **Sharing pays:** the network has **340,908** connections but only **60,000** trainable parameters.\n- **Result:** **0.95%** test error on handwritten digits (MNIST), **0.8%** when trained with extra distorted images."
  outcome: "**Bigger, deeper and trained on GPUs.**\n\n- **AlexNet (2012):** **Krizhevsky, Sutskever and Hinton** trained **5** convolutional and **3** fully connected layers, **60 million** parameters, with **ReLU**, **dropout** and data augmentation, for **5 to 6 days** on **two GTX 580** GPUs.\n- **ILSVRC-2012:** averaging **seven** such networks gave a **15.3%** top-5 test error, against **26.2%** for the second-best entry.\n- **VGG (2014):** **Simonyan and Zisserman** used only **3 × 3** filters and report a significant improvement by going to **16 to 19** weight layers.\n- **GoogLeNet (2014):** a **22-layer** network that won ILSVRC 2014 classification with **6.67%** top-5 error.\n- **Vision Transformer (2020):** Dosovitskiy et al. report that with enough data, **14M to 300M** images, a Transformer without these built-in assumptions approaches or beats the state of the art on several image benchmarks."

key_equation:
  latex: "y_{i,j} = f\\!\\left(b + \\sum_{m}\\sum_{n} K_{m,n}\\, x_{i+m,\\,j+n}\\right)"
  plain: "In words: lay the small grid of weights K over the patch of the input whose corner is at row i, column j; multiply each weight by the value under it; add them up with the bias b; pass the sum through the activation f. Moving the patch one step and repeating fills the whole feature map, with the same K and b at every position. The worked example above does this with a 2 × 2 filter."
  terms:
    - { symbol: "y_{i,j}", meaning: "one value of the output feature map: how strongly the filter's pattern is present at that position" }
    - { symbol: "x", meaning: "the input: the image's pixels, or the feature maps of the layer below" }
    - { symbol: "K_{m,n}", meaning: "the filter (kernel): a small grid of learned weights shared by every position; 5 × 5 in LeNet-5's first layer, 11 × 11 × 3 in AlexNet's" }
    - { symbol: "b", meaning: "the bias, one learned number per feature map" }
    - { symbol: "f", meaning: "the activation: a scaled tanh in LeNet-5, ReLU (negative values set to 0) in AlexNet" }

strengths:
  - "**Few parameters for the work done:** LeNet-5 has **340,908** connections but only **60,000** trainable parameters, because all units of a feature map share one set of weights."
  - "**Follows a shifted input:** if the input image is shifted, the feature map is **shifted by the same amount** and otherwise unchanged, which LeCun et al. name as the basis of its robustness to shifts and distortions."
  - "**Learns its own features:** all weights are learned by backpropagation, so the network, in LeCun et al.'s words, synthesises its own **feature extractor** instead of using a hand-designed one."
  - "**More accurate on digits:** on MNIST, LeNet-5 reaches **0.95%** test error, against **4.7%** for a fully connected network with one hidden layer of 300 units and **3.05%** for one with two hidden layers."
  - "**Depth pays:** the AlexNet authors report that removing **any** of the middle convolutional layers costs about **2%** of top-1 accuracy."

weaknesses:
  - "**Its built-in assumptions matter less with more data:** the Vision Transformer authors write that CNNs build in **locality** and **translation equivariance**, and report that with **14M to 300M** training images large-scale training **trumps** these built-in biases."
  - "**Exact position is blurred on purpose:** sub-sampling lowers the resolution of each map, which LeCun et al. describe as reducing the **precision** with which feature positions are encoded."
  - "**Each layer sees only a little more:** with 3 × 3 filters, the VGG authors note, **two** stacked layers see a 5 × 5 patch and **three** see 7 × 7, so a view of the whole image takes many layers."
  - "**Costly at scale:** the AlexNet authors write that CNNs had been **prohibitively expensive** on large high-resolution images until GPUs, and that their network's size was limited mainly by GPU memory and training time."
  - "**Fixed input size:** AlexNet needs a constant input size, so every image was rescaled and cropped to **256 × 256** before 224 × 224 patches were cut from it."

visual:
  kind: convolution
  image:
    - [0, 0, 1, 1, 0]
    - [0, 0, 1, 1, 0]
    - [0, 0, 1, 1, 0]
    - [0, 0, 1, 1, 0]
    - [0, 0, 1, 1, 0]
  filter:
    - [-1, 1]
    - [-1, 1]
  bias: 0
  pool: 2
  window: { row: 1, column: 2 }
  caption: "The worked example computed step by step: the 2 × 2 filter slid over the 5 × 5 image gives the 4 × 4 feature map, ReLU sets its negative values to 0, and 2 × 2 max-pooling keeps the largest value of each block. The outlined cells follow the window worked by hand (rows 1–2, columns 2–3) through every stage. The image and the filter are an example, not from a trained model."

comparison:
  before: "Fully connected network"
  after: "Convolutional network (LeNet-5)"
  rows:
    - { aspect: "What one hidden unit is connected to", before: "Every pixel of the input", after: "A 5 × 5 neighbourhood: 25 inputs plus a bias", source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf" }
    - { aspect: "Weights of different units", before: "Each unit has its own", after: "All units of a feature map share the same 25 weights and bias", source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf" }
    - { aspect: "Number of weights", before: "100 hidden units on an image already need several tens of thousands", after: "The whole of LeNet-5: 60,000 trainable parameters for 340,908 connections", source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf" }
    - { aspect: "When the input image shifts", before: "No built-in invariance to translations", after: "The feature map shifts by the same amount; sub-sampling adds some tolerance", source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf" }
    - { aspect: "Use of the image's layout", before: "None: pixels could be fed in any fixed order", after: "Receptive fields are local, so neighbouring pixels are read together", source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf" }
    - { aspect: "MNIST test error", before: "4.7% (one hidden layer of 300 units), 3.05% (two hidden layers, 300 and 100 units)", after: "0.95%; 0.8% with distorted training images", source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf" }

key_numbers:
  columns: ["Model", "Input", "Layers with weights", "Trainable parameters", "Training", "Reported error"]
  rows:
    - { cells: ["LeNet-5 (1998)", "32 × 32 pixels", "7", "about 60,000", "60,000 MNIST images; 2 to 3 days on one 200 MHz CPU", "0.95% MNIST test (0.8% with distorted training images)"], source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf" }
    - { cells: ["AlexNet (2012)", "224 × 224 × 3", "8 (5 convolutional, 3 fully connected)", "60 million", "1.2 million ImageNet images; 5 to 6 days on two GTX 580 3GB GPUs", "18.2% top-5 (one network, validation); 15.3% top-5 test (seven averaged), ILSVRC-2012"], source: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html" }
    - { cells: ["VGG, configuration E (2014)", "224 × 224 RGB", "19 (16 convolutional, 3 fully connected)", "144 million", "2 to 3 weeks per network on four NVIDIA Titan Black GPUs", "7.0% top-5 test for a single network; 6.8% for two combined"], source: "https://arxiv.org/abs/1409.1556" }
    - { cells: ["GoogLeNet (2014)", "224 × 224 RGB", "22", "about 12 times fewer than AlexNet", "estimated a week on a few high-end GPUs (trained on CPUs)", "6.67% top-5 test (seven networks, 144 crops each), ILSVRC 2014"], source: "https://arxiv.org/abs/1409.4842" }

misunderstandings:
  - misunderstanding: "Convolution makes a CNN completely blind to where things are (shift-invariant)."
    truth: "Convolution itself is **shift-equivariant**: LeCun et al. write that if the input is shifted, the feature map is **shifted by the same amount**. Only the sub-sampling layers add tolerance, and they describe it as **some degree** of shift, scale and distortion invariance. The worked example shows a one-pixel shift that pooling absorbs and one that it does not."
    source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf"
  - misunderstanding: "AlexNet's 15.3% ILSVRC-2012 result came from one network."
    truth: "The paper reports **18.2%** top-5 error for one network (validation). **15.3%** is the test error of an **average of seven** networks, two of them first trained on the whole ImageNet Fall 2011 release (**15M images, 22K categories**). The second-best entry had **26.2%**."
    source: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
  - misunderstanding: "Convolutional networks began with AlexNet in 2012."
    truth: "LeCun et al. trained a convolutional network by backpropagation on **US Postal Service zip code digits in 1989**, and LeNet-5 was published in **1998**. Its authors trace the convolution and sub-sampling pair to **Fukushima's Neocognitron**. AlexNet's change was **scale**: 60 million parameters, 1.2 million images and two GPUs."
    source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf"
  - misunderstanding: "To see a larger area, a CNN needs larger filters."
    truth: "Stacking small filters widens the view: the VGG authors note that **three** 3 × 3 layers see a **7 × 7** area. With C channels in and out, the stack has **27C²** weights against **49C²** for one 7 × 7 layer (**81% more**), and it applies three non-linearities instead of one."
    source: "https://arxiv.org/abs/1409.1556"

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
  <text class="d-note" x="450" y="428" text-anchor="middle">LeNet-5 (LeCun et al., 1998): tanh-type squashing in every layer up to F6; each output unit scores its digit by distance from F6 (Euclidean RBF)</text>
  </svg>
diagram_caption: "LeNet-5: a 5×5 window slides over the 32×32 input, each position giving one unit of a feature map; convolution and 2×2 subsampling shrink the maps (28→14→10→5) while their number grows (6→16), then 120, 84 and 10 units classify the digit."
notable_models:
  - { name: "LeNet-5", year: 1998, note: "LeCun, Bottou, Bengio and Haffner's 7-layer convolutional network for handwritten digits: 60,000 trainable parameters, 0.95% MNIST test error." }
  - { name: "AlexNet", year: 2012, note: "5 convolutional and 3 fully connected layers, 60 million parameters, ReLU, dropout and two GPUs; an average of seven such networks won ILSVRC-2012 with a 15.3% top-5 test error against 26.2% for the second-best entry." }
  - { name: "VGG", year: 2014, note: "The authors report a significant improvement by pushing depth to 16–19 weight layers with very small (3×3) filters; second place in ILSVRC 2014 classification." }
  - { name: "GoogLeNet / Inception", year: 2014, note: "A 22-layer network whose Inception design increases depth and width while keeping the computational budget constant; won ILSVRC 2014 classification with 6.67% top-5 error." }

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
updated: '2026-11-23'
sources:
  - { url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html", title: "ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)", trust: 0.95 }
  - { url: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf", title: "Gradient-Based Learning Applied to Document Recognition (LeCun, Bottou, Bengio & Haffner, Proceedings of the IEEE 86(11), 1998; doi:10.1109/5.726791)", trust: 0.95 }
  - { url: "http://yann.lecun.com/exdb/publis/pdf/lecun-89e.pdf", title: "Backpropagation Applied to Handwritten Zip Code Recognition (LeCun et al., Neural Computation 1(4), 541-551, 1989; doi:10.1162/neco.1989.1.4.541)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks for Large-Scale Image Recognition (Simonyan & Zisserman, arXiv:1409.1556)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.4842", title: "Going Deeper with Convolutions (Szegedy et al., arXiv:1409.4842)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2010.11929", title: "An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale (Dosovitskiy et al., arXiv:2010.11929)", trust: 0.95 }
---

## What Changed

A **fully connected** network wires every pixel to every hidden unit and gives each connection its
own weight. A **convolutional** network wires each unit to a small patch and reuses one set of
weights across the whole image.

How to read the table above (it restates Section II of LeCun et al., 1998):

- **Receptive field:** the patch of the layer below that one unit can see; 5 × 5 pixels in LeNet-5's
  first layer.
- **Feature map:** the grid of outputs of one filter, one value per position it visited.
- **Parameters** are the learned numbers; **connections** count every input-to-unit link. Sharing
  makes LeNet-5 have far more connections (340,908) than parameters (60,000).
- **MNIST** is a set of handwritten digits, 60,000 for training and 10,000 for testing; the error is
  the share of test digits classified wrongly.

## Key Ideas

- **Local receptive fields:** each unit reads a small neighbourhood, where simple features such as
  edges and corners can be found.
- **Shared weights:** one filter is applied at every position, so a feature learned in one place is
  found everywhere, with far fewer parameters.
- **Sub-sampling (pooling):** lowering the resolution of each map makes the exact position of a
  feature matter less.
- **Alternate and stack:** convolution and sub-sampling layers alternate; the maps shrink while
  their number grows (6, then 16, then 120 in LeNet-5).

## How It Works

The figure above is **LeNet-5**, read from left to right. Above each block is its name and size:
"6 @ 28×28" means six feature maps of 28 by 28 values.

A **pixel** is one number of the image grid. A **filter** (or kernel) is a small grid of learned
weights; the **squashing function** of LeNet-5 is a scaled tanh, f(a) = 1.7159 · tanh(S · a).

1. **Input: 32 × 32 pixels.** The digits are at most 20 × 20 pixels, centred in a larger field, so
   that strokes near the edge can still fall at the centre of the top layers' receptive fields.
2. **C1, convolution.** A **5 × 5 window** slides one step at a time (the accent square on the
   input). At each position, each of **6 filters** multiplies the 25 pixels by its 25 weights, adds
   its bias and squashes the sum. The window fits 32 − 5 + 1 = **28** positions across, so each map
   is 28 × 28. C1 has 6 × (25 + 1) = **156** parameters and 28 × 28 × 6 × 26 = **122,304**
   connections.
3. **S2, sub-sampling.** Each map is cut into non-overlapping **2 × 2** blocks. The four values of a
   block are averaged, multiplied by a learned coefficient, a learned bias is added and the result
   is squashed: 6 maps of **14 × 14**, with only 12 parameters.
4. **C3, convolution.** **16** filters of 5 × 5 give maps of 14 − 5 + 1 = **10 × 10**. Each C3 map
   reads only **a subset** of the 6 S2 maps ("sparse map links"); the authors give two reasons: it
   bounds the number of connections and makes different maps learn different features.
5. **S4, sub-sampling.** The same 2 × 2 step: 16 maps of **5 × 5**.
6. **C5, convolution.** **120** filters of 5 × 5 over all 16 maps. A 5 × 5 filter on a 5 × 5 map fits
   in one position only, so each map is **1 × 1** and C5 acts as a full connection.
7. **F6, full connection.** **84** units, each a weighted sum of all 120 values plus a bias, then
   squashed.
8. **Output: 10 RBF units.** Each radial basis function (RBF) unit computes the **squared distance**
   between the 84 values of F6 and a fixed pattern for its digit (a 7 × 12 bitmap, hence 84). The
   smaller the output, the better the match.
9. **What later networks changed.** AlexNet keeps the convolutions but uses **ReLU** in place of the
   squashing function, **3 × 3 max-pooling** with stride 2 in place of averaging, and a
   **1000-way softmax** in place of the RBF units.

## Worked Example

**One filter, one feature map, one pooling step**, on a 5 × 5 image. The image and the filter are
made up so every step can be checked by hand: an example, not from a trained model.

The image (0 is dark, 1 is bright) has a bright vertical bar in columns 3 and 4; all five rows are
the same:

| Column | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- |
| Every row | 0 | 0 | 1 | 1 | 0 |

The filter is 2 × 2, with bias 0. Its rows are (−1, 1) and (−1, 1): it scores **+2** where dark
turns to bright from left to right, and **−2** where bright turns to dark.

1. **One window by hand.** Place the filter over rows 1–2, columns 2–3. The pixels are (0, 1) in
   both rows:
   - (−1 × 0) + (1 × 1) + (−1 × 0) + (1 × 1) = **2**
2. **Slide across.** A 2 × 2 window fits 5 − 2 + 1 = **4** positions in each direction, so the
   feature map is 4 × 4. Along a row the windows cover columns 1–2, 2–3, 3–4 and 4–5:
   - columns 1–2, pixels (0, 0): 0 + 0 = **0**
   - columns 2–3, pixels (0, 1): **2** (step 1)
   - columns 3–4, pixels (1, 1): −1 + 1 − 1 + 1 = **0**
   - columns 4–5, pixels (1, 0): −1 + 0 − 1 + 0 = **−2**
   - Every row of the image is the same, so every row of the map is **(0, 2, 0, −2)**.
3. **ReLU.** AlexNet's activation sets negative values to 0: each row becomes **(0, 2, 0, 0)**. The
   map now marks only the left edge of the bar.
4. **2 × 2 max-pooling** (the pooling VGG uses, with stride 2). Take the largest value of each non-overlapping 2 × 2 block: max(0, 2) = 2
   and max(0, 0) = 0, so the pooled map has rows **(2, 0)**.
5. **Count the weights.** The filter's 4 weights and 1 bias were used at all **16** positions. A
   fully connected layer giving the same 16 outputs from 25 pixels would need 25 × 16 = **400**
   weights.
6. **Shift the bar one column left** (columns 2–3). The map rows become (2, 0, −2, 0); after ReLU
   (2, 0, 0, 0); after pooling **(2, 0)**, the same as before. The map moved with the bar, and
   pooling hid the move.
7. **Shift the bar one column right instead** (columns 4–5). The map rows become (0, 0, 2, 0),
   unchanged by ReLU; after pooling **(0, 2)**. This time the pooled output changed.

**What this means:** one shared 2 × 2 filter found the bar's edge at every position with 5 numbers
instead of 400. When the image shifts, the feature map shifts with it; pooling absorbs some shifts
and not others, which is why LeCun et al. speak of **some degree** of shift invariance.
