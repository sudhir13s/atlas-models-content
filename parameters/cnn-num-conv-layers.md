---
id: parameter/cnn-num-conv-layers
type: parameter
slug: cnn-num-conv-layers
schema_version: 2.9
title: "Number of Convolutional Layers (Depth)"
name: num_conv_layers
priority: 2
importance: critical
applies_to: [cnn]
range: "3 — 151"
controls: "How many convolutional layers are stacked between the input image and the classifier: the depth of the network."
effects:
  decrease:
    - "Fewer parameters by exactly the layers removed; each layer adds its own count"
    - "A smaller view: each 3 × 3 layer widens what one unit sees by 2 cells"
    - "In VGG's tests error rose with less depth: 29.6% top-1 for the 11-layer net A against 27.0% for the 16-layer net D"
  increase:
    - "Each added layer adds its own parameters: from VGG's net D to net E, three more layers add 5,309,696"
    - "In VGG error fell from 11 to 16 layers and then saturated: net E (19 layers) gave 27.3% against 27.0% for D"
    - "Plain stacks degrade: ResNet's authors found a 34-layer plain net had higher training error than an 18-layer one; shortcuts removed the problem"
scale:
  min: 2
  max: 200
  typical_min: 3
  typical_max: 151
  spacing: log
typical_values:
  - setting: "LeNet-5: C1, C3 and C5 (LeCun et al., 1998)"
    value: "3"
    source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf"
  - setting: "AlexNet (AlexNet paper)"
    value: "5"
    source: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
  - setting: "VGG net A, 11 weight layers (VGG paper, Table 1)"
    value: "8"
    source: "https://arxiv.org/abs/1409.1556"
  - setting: "VGG-16, net D (VGG paper, Table 1)"
    value: "13"
    source: "https://arxiv.org/abs/1409.1556"
  - setting: "VGG-19, net E (VGG paper, Table 1)"
    value: "16"
    source: "https://arxiv.org/abs/1409.1556"
  - setting: "ResNet-50, main path (ResNet paper, Table 1)"
    value: "49"
    source: "https://arxiv.org/abs/1512.03385"
  - setting: "ResNet-152, main path (ResNet paper, Table 1)"
    value: "151"
    source: "https://arxiv.org/abs/1512.03385"
mistakes:
  - mistake: "Assuming a deeper network has more parameters"
    why: >-
      In VGG-16 the 13 convolutional layers hold **14,714,688** parameters and the 3 fully connected
      layers **123,642,856**, 89% of the total. torchvision gives ResNet-152 **60,192,808** parameters and
      ResNet-50 **25,557,032**, against **138,357,544** for VGG-16.
    fix: "Count the layers' sizes and the head, not the depth alone."
  - mistake: "Blaming vanishing gradients for deep plain networks doing worse"
    why: >-
      The ResNet authors argue the degradation is unlikely to be caused by vanishing gradients: their
      plain nets use batch normalization and they checked that the backward gradients have healthy
      norms. The deeper plain net had higher **training** error, so overfitting is not the cause either.
    fix: "Read deep plain stacks as hard to optimise; residual shortcuts are the paper's remedy."
  - mistake: "Taking the number in the name as the convolution count"
    why: >-
      VGG-16 has 13 convolutional and 3 fully connected layers. ResNet-50 counts 1 + 3 × 16 = 49
      convolutions on its main path plus the fully connected layer; its 1 × 1 projection shortcuts are
      not in the 50. LeNet-5's 7 layers include its two sub-sampling layers, which have trainable
      parameters.
    fix: "Count the layers from the architecture table."
related: [parameter/cnn-num-filters, parameter/cnn-kernel-size, parameter/cnn-pooling, parameter/cnn-batch-norm]
visual:
  kind: conv_params
  groups:
    - label: "Net A: 8 conv + 3 FC"
      layers:
        - { kernel: 3, in_channels: 3, out_channels: 64, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 64, out_channels: 128, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 128, out_channels: 256, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 256, out_channels: 256, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 256, out_channels: 512, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 512, out_channels: 512, count: 3, bias: true, part: "conv" }
        - { kernel: 7, in_channels: 512, out_channels: 4096, count: 1, bias: true, part: "FC" }
        - { kernel: 1, in_channels: 4096, out_channels: 4096, count: 1, bias: true, part: "FC" }
        - { kernel: 1, in_channels: 4096, out_channels: 1000, count: 1, bias: true, part: "FC" }
    - label: "Net B: 10 conv + 3 FC"
      layers:
        - { kernel: 3, in_channels: 3, out_channels: 64, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 64, out_channels: 64, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 64, out_channels: 128, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 128, out_channels: 128, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 128, out_channels: 256, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 256, out_channels: 256, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 256, out_channels: 512, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 512, out_channels: 512, count: 3, bias: true, part: "conv" }
        - { kernel: 7, in_channels: 512, out_channels: 4096, count: 1, bias: true, part: "FC" }
        - { kernel: 1, in_channels: 4096, out_channels: 4096, count: 1, bias: true, part: "FC" }
        - { kernel: 1, in_channels: 4096, out_channels: 1000, count: 1, bias: true, part: "FC" }
    - label: "Net D: 13 conv + 3 FC"
      layers:
        - { kernel: 3, in_channels: 3, out_channels: 64, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 64, out_channels: 64, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 64, out_channels: 128, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 128, out_channels: 128, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 128, out_channels: 256, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 256, out_channels: 256, count: 2, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 256, out_channels: 512, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 512, out_channels: 512, count: 5, bias: true, part: "conv" }
        - { kernel: 7, in_channels: 512, out_channels: 4096, count: 1, bias: true, part: "FC" }
        - { kernel: 1, in_channels: 4096, out_channels: 4096, count: 1, bias: true, part: "FC" }
        - { kernel: 1, in_channels: 4096, out_channels: 1000, count: 1, bias: true, part: "FC" }
    - label: "Net E: 16 conv + 3 FC"
      layers:
        - { kernel: 3, in_channels: 3, out_channels: 64, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 64, out_channels: 64, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 64, out_channels: 128, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 128, out_channels: 128, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 128, out_channels: 256, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 256, out_channels: 256, count: 3, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 256, out_channels: 512, count: 1, bias: true, part: "conv" }
        - { kernel: 3, in_channels: 512, out_channels: 512, count: 7, bias: true, part: "conv" }
        - { kernel: 7, in_channels: 512, out_channels: 4096, count: 1, bias: true, part: "FC" }
        - { kernel: 1, in_channels: 4096, out_channels: 4096, count: 1, bias: true, part: "FC" }
        - { kernel: 1, in_channels: 4096, out_channels: 1000, count: 1, bias: true, part: "FC" }
  caption: "VGG nets A, B, D (VGG-16) and E (VGG-19) from the VGG paper's Table 1, counted layer by layer: conv = the 3 × 3 convolutional layers, FC = the three fully connected layers, written as the 7 × 7 and 1 × 1 convolutions the paper converts them to at test time. The totals, 132,863,336, 133,047,848, 138,357,544 and 143,667,240, match torchvision's counts and the paper's 133, 133, 138 and 144 million; 8 to 16 convolutional layers change the count far less than the head holds."
tags: [cnn, convolution, depth, structure, parameters, vision]
updated: 2026-11-23
sources:
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks for Large-Scale Image Recognition (Simonyan & Zisserman, arXiv:1409.1556)", trust: 0.95 }
  - { url: "https://github.com/pytorch/vision/blob/main/torchvision/models/vgg.py", title: "torchvision source: models/vgg.py", trust: 0.9 }
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (He, Zhang, Ren & Sun, arXiv:1512.03385)", trust: 0.95 }
  - { url: "https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py", title: "torchvision source: models/resnet.py", trust: 0.9 }
  - { url: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf", title: "Gradient-Based Learning Applied to Document Recognition (LeCun, Bottou, Bengio & Haffner, Proceedings of the IEEE 86(11), 1998)", trust: 0.95 }
  - { url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html", title: "ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)", trust: 0.95 }
---

## Definition

**The number of convolutional layers is the depth of the network's feature extractor**: how many
convolutions the image passes through before the classifier.

- **Each layer** reads the maps of the layer below with its own filters and weights.
- **Depth widens the view:** VGG notes that two 3 × 3 layers see a 5 × 5 patch and three see 7 × 7.
- **Depth adds parameters layer by layer**, each with its own count; it is not one setting in
  PyTorch but the length of the stack.
- **The figure** counts VGG's nets of 8 to 16 convolutional layers.

**Analogy:** floors of a building, each looking at the floor below and summarising it for the next.
The analogy shows the idea only; the counts are under Calculation.

## How It Works

**Stack convolutions, shrink the maps now and then, classify at the end.**

1. **Early layers** read the image: in VGG two layers of 64 filters at 224 × 224.
2. **Downsampling** between blocks halves the maps: max pooling in VGG, stride-2 convolutions in
   ResNet.
3. **Later layers** read wider regions with more filters, up to 512 in VGG.
4. **The head** turns the last maps into class scores: three fully connected layers in VGG, global
   average pooling and one fully connected layer in ResNet.
5. **Very deep stacks** need help: ResNet adds a shortcut around every two or three layers.

## Calculation

**The depth is the number of terms in the sum.**

In words: the network's convolutional parameters are each layer's count added up, and the head is
added on top.

$$
P = \sum_{\ell=1}^{L} \left( k_\ell^2 \, C_{\ell-1} \, C_\ell + C_\ell \right) + P_\text{head}
$$

| Symbol | Meaning |
| --- | --- |
| $L$ | number of convolutional layers |
| $k_\ell$, $C_\ell$ | kernel size and filters of layer ℓ |
| $C_0$ | input channels: 3 for colour images |
| $P_\text{head}$ | the classifier after the convolutions |

For whole-network counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**VGG-16 (net D), counted from the paper's Table 1** with torchvision's biases.

1. **13 convolutional layers:** 1,792 + 36,928 + 73,856 + 147,584 + 295,168 + 2 × 590,080 +
   1,180,160 + 5 × 2,359,808 = **14,714,688**.
2. **First fully connected layer:** 7 × 7 × 512 inputs, 25,088 × 4,096 + 4,096 = **102,764,544**.
3. **Second and third:** 4,096 × 4,096 + 4,096 = 16,781,312 and 4,096 × 1,000 + 1,000 = 4,097,000.
4. **Head total:** **123,642,856**, 89% of the network.
5. **Network total:** 14,714,688 + 123,642,856 = **138,357,544**, torchvision's count and the paper's
   138 million.
6. **Three more layers (net E):** conv3_4 (590,080) and conv4_4, conv5_4 (2,359,808 each) add
   **5,309,696**, giving 143,667,240.
7. **ResNet-50 for comparison:** 1 + 3 × 16 = 49 convolutions on the main path, **25,557,032**
   parameters in all (torchvision).

**What this means:** depth adds layer counts one by one, but where the parameters sit depends far
more on the widths and the head than on how many layers there are.

## What To Watch

- **The head.** Fully connected layers on a 7 × 7 × 512 map dwarf every convolution.
- **Shortcuts.** Beyond a few tens of layers, check for residual connections (ResNet).
- **What is counted.** Names count weight layers, sometimes including fully connected or
  sub-sampling layers.
