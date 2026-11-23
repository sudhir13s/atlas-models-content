---
id: parameter/cnn-conv-stride
type: parameter
slug: cnn-conv-stride
schema_version: 3.1
title: Convolution Stride
name: stride
priority: 9
importance: optional
applies_to:
- cnn
range: 1 — 4
controls: How many cells the filter moves between one position and the next; above 1 it shrinks the output.
effects:
  decrease:
  - 'A larger output: at stride 1 the window visits every position'
  - More positions to compute, in this layer and in every layer after it
  - No change in this layer's parameters
  increase:
  - 'The output side is roughly divided by the stride: ResNet''s conv1 turns 224 into 112 at stride 2'
  - 'Fewer positions to compute: a quarter at stride 2'
  - No change in this layer's parameters; a fully connected layer after it receives fewer inputs
scale:
  min: 1
  max: 6
  typical_min: 1
  typical_max: 4
  spacing: linear
typical_values:
- setting: VGG, every convolution (VGG paper)
  value: '1'
  source: https://arxiv.org/abs/1409.1556
- setting: ResNet, conv1 and the first convolution of conv3_x, conv4_x and conv5_x (ResNet paper)
  value: '2'
  source: https://arxiv.org/abs/1512.03385
- setting: AlexNet, first convolutional layer (AlexNet paper)
  value: '4'
  source: https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html
mistakes:
- mistake: Thinking a larger stride makes the layer smaller
  why: PyTorch's Conv2d weight has the shape out_channels × in_channels × k × k, with no stride in it. The stride-2 3 × 3 convolution that opens ResNet-34's conv3_x has 3 × 3 × 64 × 128 = **73,728** weights, exactly as many as at stride 1. Only the output, and what reads it, gets smaller.
  fix: Look for the saving in the computation and in later fully connected layers, not in this layer.
- mistake: Assuming stride s always divides the size by exactly s
  why: The output side is ⌊(i + 2p − d(k − 1) − 1) ÷ s⌋ + 1 (PyTorch's documentation). ResNet's 7 × 7 conv1 at stride 2 gives **112** from 224 only with torchvision's padding of 3; with no padding it gives **109**.
  fix: Compute the size with the formula, padding included.
- mistake: Assuming a network needs pooling to shrink its maps
  why: 'ResNet downsamples with stride-2 convolutions. Springenberg et al. replaced max-pooling with a stride-2 convolution in their CIFAR-10 nets: All-CNN-C reached **9.08%** error against **9.74%** for base model C. Removing the pooling and only raising the previous layer''s stride gave **10.19%**.'
  fix: Treat a strided convolution and pooling as two ways to downsample, and compare them on results.
related:
- parameter/cnn-padding
- parameter/cnn-pooling
- parameter/cnn-kernel-size
- parameter/cnn-dilation
visual:
  kind: conv_geometry
  image:
  - - 3
    - 1
    - 0
    - 2
    - 1
  - - 1
    - 4
    - 2
    - 0
    - 3
  - - 0
    - 2
    - 5
    - 1
    - 0
  - - 2
    - 0
    - 1
    - 3
    - 2
  - - 1
    - 3
    - 0
    - 2
    - 4
  filter:
  - - 1
    - 0
    - 1
  - - 0
    - 2
    - 0
  - - 1
    - 0
    - 1
  panels:
  - label: Stride 1
    stride: 1
    padding: 0
    dilation: 1
    window:
      row: 1
      column: 2
  - label: Stride 2
    stride: 2
    padding: 0
    dilation: 1
    window:
      row: 1
      column: 2
  caption: 'The same 3 × 3 filter on the same 5 × 5 grid at stride 1 and stride 2, no padding. The outlined window gives output cell (1, 2): at stride 1 it starts one column right of the first window, at stride 2 two columns right, so the output shrinks from 3 × 3 to 2 × 2. The grid and filter are an example, not from a trained model.'
tags:
- cnn
- convolution
- stride
- downsampling
- vision
updated: '2026-11-23'
sources:
- url: https://docs.pytorch.org/docs/stable/generated/torch.nn.Conv2d.html
  title: 'PyTorch documentation: torch.nn.Conv2d'
  trust: 0.9
- url: https://arxiv.org/abs/1512.03385
  title: Deep Residual Learning for Image Recognition (He, Zhang, Ren & Sun, arXiv:1512.03385)
  trust: 0.95
- url: https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py
  title: 'torchvision source: models/resnet.py'
  trust: 0.9
- url: https://arxiv.org/abs/1409.1556
  title: Very Deep Convolutional Networks for Large-Scale Image Recognition (Simonyan & Zisserman, arXiv:1409.1556)
  trust: 0.95
- url: https://proceedings.neurips.cc/paper/2012/hash/c399862d3b9d6b76c8436e924a68c45b-Abstract.html
  title: ImageNet Classification with Deep Convolutional Neural Networks (Krizhevsky, Sutskever & Hinton, NeurIPS 2012)
  trust: 0.95
- url: https://arxiv.org/abs/1412.6806
  title: 'Striving for Simplicity: The All Convolutional Net (Springenberg, Dosovitskiy, Brox & Riedmiller, arXiv:1412.6806)'
  trust: 0.9
- url: https://arxiv.org/abs/1603.07285
  title: A guide to convolution arithmetic for deep learning (Dumoulin & Visin, arXiv:1603.07285)
  trust: 0.9
---

## Definition

**stride is how many cells the filter moves between one position and the next.** PyTorch's
documentation says it "controls the stride for the cross-correlation".

- **Stride 1** puts the window on every position, so neighbouring windows overlap.
- **Stride 2** skips every other position, so the output has about half the rows and columns.
- **It holds no weights:** the filter is the same; only where it is placed changes.
- **The figure** shows the window that gives output cell (1, 2) at both strides.

**Analogy:** reading a line with a ruler, moving it one letter at a time or two letters at a time.
The analogy shows the idea only; the exact size rule is under Calculation.

## How It Works

**Place, compute, jump by the stride.**

1. **First position.** The window starts at the top-left corner of the (padded) input.
2. **Compute.** Weights times values, summed, plus the bias, give one output number.
3. **Jump.** The window moves s cells right; at the end of a row it moves s cells down.
4. **Shrink.** Fewer positions fit, so the output map is smaller; ResNet uses this in place of
   pooling inside its stages ("downsampling directly by convolutional layers that have a stride
   of 2").

## Calculation

**The stride sets the output size, not the parameter count.**

In words: the span the window can travel is divided by the stride; one more is added for the
first position, and the result is rounded down.

$$
o = \left\lfloor \frac{i + 2p - d\,(k-1) - 1}{s} \right\rfloor + 1 \qquad P = k^2 \, C_\text{in} \, C_\text{out} + C_\text{out}
$$

| Symbol | Meaning |
| --- | --- |
| $s$ | stride |
| $i$, $o$ | input and output side |
| $k$, $p$, $d$ | kernel size, padding, dilation |
| $C_\text{in}$, $C_\text{out}$ | input channels, filters |

$P$ has no $s$ in it. For whole-network counts, see the
[parameter calculators](/models/hyperparameters/calculators).

## Worked Example

**The figure's grid, then ResNet-34's first stages** (from the ResNet paper's Table 1 and
torchvision's code).

1. **Stride 1 on the 5 × 5 grid:** ⌊(5 + 0 − 1 × 2 − 1) ÷ 1⌋ + 1 = 2 + 1 = **3**.
2. **Stride 2:** ⌊2 ÷ 2⌋ + 1 = **2**. Output cell (1, 2) now reads columns 3 to 5, not 2 to 4.
3. **ResNet conv1** (7 × 7, stride 2, padding 3): ⌊(224 + 6 − 6 − 1) ÷ 2⌋ + 1 = 111 + 1 = **112**.
4. **Max pooling** (3 × 3, stride 2, padding 1): ⌊(112 + 2 − 2 − 1) ÷ 2⌋ + 1 = **56**.
5. **First convolution of conv3_x** (3 × 3, stride 2, padding 1): ⌊(56 + 2 − 2 − 1) ÷ 2⌋ + 1 = **28**.
6. **Its parameters:** 3 × 3 × 64 × 128 = **73,728**, no bias, the same at stride 1.
7. **Its positions:** 28 × 28 = **784** against 56 × 56 = 3,136 at stride 1, a quarter.

**What this means:** the stride costs nothing in weights; it trades resolution for computation, and
the sizes 112, 56 and 28 in the paper's table follow from the formula.

## What To Watch

- **Padding with the stride.** Same padding in PyTorch (padding='same') does not support strides
  other than 1.
- **Rounding.** The formula rounds down, so cells the last window cannot reach are never read: a
  2 × 2 window at stride 2 on a 5 × 5 grid skips the fifth row and column.
- **What follows.** A fully connected layer after the maps gets fewer inputs and fewer weights.
