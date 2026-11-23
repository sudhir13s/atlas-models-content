---
id: parameter/cnn-pooling
type: parameter
slug: cnn-pooling
schema_version: 2.9
title: "Pooling (Max / Average)"
name: pool_size
priority: 8
importance: optional
applies_to: [cnn]
range: "2 — 7"
controls: "The size of the window that pooling summarises into one number, by its largest value (max) or its mean (average); in PyTorch the kernel_size of MaxPool2d or AvgPool2d."
effects:
  decrease:
    - "Each output summarises fewer values, so more detail survives"
    - "With the stride equal to the size, a smaller window shrinks the map less"
  increase:
    - "Each output summarises more values; the stride, not the size alone, sets the output size"
    - "AlexNet's overlapping 3 × 3 windows at stride 2 reduced top-1 and top-5 error by 0.4% and 0.3% against 2 × 2 windows at stride 2, which give an output of the same size"
    - "A window as large as the map (global average pooling) leaves one number per channel: ResNet's head then needs 2,049,000 parameters, VGG's fully connected head 123,642,856"
scale:
  min: 1
  max: 8
  typical_min: 2
  typical_max: 7
  spacing: linear
typical_values:
  - setting: "LeNet-5, sub-sampling layers S2 and S4: average, times a learned coefficient, plus a bias (LeCun et al., 1998)"
    value: "2"
    source: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf"
  - setting: "VGG, max pooling with stride 2 (VGG paper)"
    value: "2"
    source: "https://arxiv.org/abs/1409.1556"
  - setting: "AlexNet, overlapping max pooling with stride 2 (AlexNet paper)"
    value: "3"
    source: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html"
  - setting: "ResNet, max pooling after conv1, stride 2 (ResNet paper, Table 1)"
    value: "3"
    source: "https://arxiv.org/abs/1512.03385"
  - setting: "ResNet, global average pooling over the last 7 × 7 maps (ResNet paper)"
    value: "7"
    source: "https://arxiv.org/abs/1512.03385"
mistakes:
  - mistake: "Assuming pooling never has parameters"
    why: >-
      PyTorch's MaxPool2d and AvgPool2d have none. LeNet-5's sub-sampling did: each unit averages its
      four inputs, multiplies by a trainable coefficient and adds a trainable bias, so S2 has **12**
      parameters for its 6 maps and S4 **32** for 16.
    fix: "Check what the layer computes; count 2 per map for LeNet-style sub-sampling."
  - mistake: "Thinking pooling makes the network blind to position"
    why: >-
      LeCun et al. describe sub-sampling as reducing the precision with which positions are encoded and
      the sensitivity to shifts, not removing it; the CNN architecture page shows a one-cell shift that
      pooling absorbs and one that it does not.
    fix: "Expect some tolerance to small shifts, not invariance."
  - mistake: "Assuming the output is the input divided by the window size"
    why: >-
      The output side is ⌊(i + 2p − size) ÷ stride⌋ + 1, and PyTorch's stride defaults to the window
      size. On the figure's 5 × 5 grid, 2 × 2 at stride 2 and 3 × 3 at stride 2 both give **2 × 2**,
      which is how AlexNet compared them at "equivalent dimensions".
    fix: "Compute the size from window, stride and padding."
related: [parameter/cnn-conv-stride, parameter/cnn-kernel-size, parameter/cnn-num-conv-layers]
visual:
  kind: pooling
  image:
    - [1, 3, 2, 0, 1]
    - [4, 6, 1, 2, 0]
    - [0, 2, 5, 7, 3]
    - [1, 1, 3, 4, 2]
    - [2, 0, 1, 3, 6]
  panels:
    - { label: "Max 2 × 2, stride 2", size: 2, stride: 2, mode: max, window: { row: 1, column: 2 } }
    - { label: "Mean 2 × 2, stride 2", size: 2, stride: 2, mode: average, window: { row: 1, column: 2 } }
    - { label: "Max 3 × 3, stride 2", size: 3, stride: 2, mode: max, window: { row: 1, column: 2 } }
  caption: "One 5 × 5 grid pooled three ways, the window of output cell (1, 2) outlined: 2 × 2 windows at stride 2 do not overlap and drop the last row and column; 3 × 3 windows at stride 2 overlap by one cell, as in AlexNet, and give the same 2 × 2 output. The grid is an example, not from a trained model."
tags: [cnn, pooling, downsampling, vision]
updated: 2026-11-23
sources:
  - { url: "https://docs.pytorch.org/docs/stable/generated/torch.nn.MaxPool2d.html", title: "PyTorch documentation: torch.nn.MaxPool2d", trust: 0.9 }
  - { url: "https://docs.pytorch.org/docs/stable/generated/torch.nn.AvgPool2d.html", title: "PyTorch documentation: torch.nn.AvgPool2d", trust: 0.9 }
  - { url: "http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf", title: "Gradient-Based Learning Applied to Document Recognition (LeCun, Bottou, Bengio & Haffner, Proceedings of the IEEE 86(11), 1998)", trust: 0.95 }
  - { url: "https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html", title: "ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1409.1556", title: "Very Deep Convolutional Networks for Large-Scale Image Recognition (Simonyan & Zisserman, arXiv:1409.1556)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1512.03385", title: "Deep Residual Learning for Image Recognition (He, Zhang, Ren & Sun, arXiv:1512.03385)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1312.4400", title: "Network In Network (Lin, Chen & Yan, arXiv:1312.4400)", trust: 0.9 }
  - { url: "https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py", title: "torchvision source: models/resnet.py", trust: 0.9 }
---

## Definition

**pool_size is the window a pooling layer summarises into one number.** Max pooling keeps the
largest value of the window; average pooling keeps the mean.

- **It works on each channel separately**, so the number of channels does not change.
- **With a stride above 1** it shrinks the maps: 2 × 2 at stride 2 keeps a quarter of the values.
- **PyTorch's pooling layers learn nothing**; LeNet-5's sub-sampling learned two numbers per map.
- **The figure** pools one grid by max, by mean, and with overlapping windows.

**Analogy:** summarising each paragraph of a report by its most striking sentence (max) or by its
general gist (mean). The analogy shows the idea only; the exact rule is under Calculation.

## How It Works

**Window, summarise, move.**

1. **Window.** A size × size window sits on the top-left corner of one channel.
2. **Summarise.** Max keeps the largest value; average adds the values and divides by their number.
3. **Move.** The window jumps by the stride (PyTorch's default is the window size) and repeats.
4. **Global average pooling** takes the whole map as the window: Lin et al. proposed it to replace
   fully connected layers, noting it has no parameter to optimise; ResNet ends with it.

## Calculation

**The output size, and the parameters it saves later.**

In words: the window travels the input in steps of the stride, rounded down; a smaller map means
fewer inputs for any fully connected layer after it.

$$
o = \left\lfloor \frac{i + 2p - z}{s} \right\rfloor + 1 \qquad P_\text{FC} = (o \cdot o \cdot C) \times n + n
$$

| Symbol | Meaning |
| --- | --- |
| $z$ | pool_size (window) |
| $s$, $p$ | stride, padding |
| $i$, $o$ | input and output side |
| $C$, $n$ | channels pooled, units of the fully connected layer after it |

For whole-network counts, see the [parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The figure's grid, then VGG's and ResNet's heads.**

1. **Max 2 × 2, stride 2, cell (1, 2):** max(2, 0, 1, 2) = **2**. Output: ⌊(5 − 2) ÷ 2⌋ + 1 = **2**
   per side; the fifth row and column are never read.
2. **Mean of the same window:** (2 + 0 + 1 + 2) ÷ 4 = **1.25**.
3. **Max 3 × 3, stride 2, cell (1, 2):** rows 1 to 3, columns 3 to 5, max(2, 0, 1, 1, 2, 0, 5, 7, 3) =
   **7**. Output: ⌊(5 − 3) ÷ 2⌋ + 1 = **2** per side, the same size.
4. **VGG's five max pools:** 224, 112, 56, 28, 14, **7**.
5. **VGG's first fully connected layer:** 7 × 7 × 512 = 25,088 inputs, 25,088 × 4,096 + 4,096 =
   **102,764,544** parameters.
6. **ResNet-50's head:** global average pooling turns 7 × 7 × 2,048 into 2,048 numbers;
   2,048 × 1,000 + 1,000 = **2,049,000**.

**What this means:** pooling itself holds no weights in PyTorch, but by shrinking the maps it
decides how many weights the classifier after it needs.

## What To Watch

- **Stride.** Equal to the size by default; smaller means overlapping windows (AlexNet).
- **Padding.** MaxPool2d pads with negative infinity, AvgPool2d with zeros that are counted in the
  mean by default (count_include_pad).
- **LeNet-style sub-sampling.** It has a coefficient and a bias per map.
