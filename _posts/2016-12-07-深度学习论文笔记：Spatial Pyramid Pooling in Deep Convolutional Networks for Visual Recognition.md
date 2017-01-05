---
layout: post
title: 深度学习论文笔记：Spatial Pyramid Pooling in Deep Convolutional Networks for Visual Recognition
date: 2016-12-07 15:32:24.000000000 +09:00
---

## Abstract

* 现有的深卷积神经网络（CNN）需要固定尺寸（例如，224×224）的输入图像。
* 新的网络结构，称为SPP-net，可以生成固定长度的表示，而不管图像大小/规模。
* 使用SPP-net，我们从整个图像只计算一次特征图，然后在任意区域（子图像）中池特征以生成固定长度表示以训练检测器。

## 1 INTRODUCTION

* 在CNN的训练和测试中存在技术问题：普遍的CNN需要固定的输入图像大小（例如，224×224），其限制了输入图像的宽高比和比例。
* Cropping
* Warping->unwanted geometric distortion(不需要的几何失真)
* 那么为什么CNN需要固定输入大小？

  * CNN主要由两部分组成：卷积层和跟随的完全连接的层。
  * 事实上，卷积层不需要固定的图像大小，并且可以生成任何大小的特征图
  * 另一方面，根据定义：完全连接的层需要具有固定尺寸/长度输入。所以固定尺寸完全来自于 **全连接层**

* 我们提出了一个spatial pyramid pooling（空间金字塔池化层）来去掉额昂罗固定输入的约束。

* 具体来说，我们在最后一个卷积层的顶部添加一个SPP层。 SPP层汇集特征并产生固定长度的输出，然后馈送到完全连接的层（或其他分类器）。

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfnxkqg21j30jl03hwf2.jpg)

* SPP对于深度CNN有着一些显著的特性：

  * 1）SPP能够生成固定长度的输出，而不管输入大小，而在以前的深度网络[3]中使用的滑动窗口池不能;
  * 2）SPP使用多级空间仓，而滑动窗口池仅使用单个窗口大小。 多层池化已被证明对于对象变形是鲁棒的[15];
  * 3）由于输入尺度的灵活性，SPP可以在可变尺度上提取的特征。

* 实验表明，这种多尺寸训练与传统的单尺寸训练一样收敛，并导致更好的测试精度。
* SPP的优点是与特定的CNN设计是正交的。
* Caltech101: L. Fei-Fei, R. Fergus, and P. Perona, “Learning generative visual models from few training examples: An incremental bayesian approach tested on 101 object categories,” CVIU, 2007.
* VOC 2007: M. Everingham, L. Van Gool, C. K. I. Williams, J. Winn, and A. Zisserman, “The PASCAL Visual Object Classes Challenge 2007 (VOC2007) Results,” 2007.
* 但是R-CNN中的特征计算是耗时的，因为它对每个图像的数千个wraped区域的原始像素重复应用深卷积网络。而本文提出的方法可以在一整张图像上只跑一次卷积层

## 2 DEEP NETWORKS WITH SPATIAL PYRAMID POOLING

* 输入图像中的这些形状激活在相应位置的feature map

### 2.2 The Spatial Pyramid Pooling Layer

* Bag-of-Words (BoW) approach->用来将生成的特征进行pool从而产生固定长度的向量。
* 空间金字塔池提高BoW，因为它可以通过在局部空间仓中汇集来 **维护空间信息** 。
* “global pooling” operation

  * a global average pooling
  * a global average pooling

### 2.3 Training the Network

* Single-size training
* Multi-size training

## 3. SPP-NET FOR IMAGE CLASSIFICATION

## 4. SPP-NET FOR OBJECT DETECTION

* 对于R-CNN来说，Feature extraction is the major timing bottleneck in testing.
* 对于我们的SPP-net来说，我们从一整张图片中值提取一次特征。
* On the contrary, our method enables feature extraction in **arbitrary windows** from the deep convolutional feature maps.

### 4.1 Detection Algorithm

![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfnxjqg0ej30h50lfai3.jpg)
