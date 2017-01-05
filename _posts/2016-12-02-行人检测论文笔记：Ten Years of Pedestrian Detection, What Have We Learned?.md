---
layout: post
title: 行人检测论文笔记：Ten Years of Pedestrian Detection, What Have We Learned?
date: 2016-12-02 15:32:24.000000000 +09:00
---

# 论文笔记：Ten Years of Pedestrian Detection, What Have We Learned?
## Abstract

* 这种新的决策林探测器在挑战性的Caltech-USA数据集上实现了当前最好的已知性能。

## 1 Introduction

* 更重要的是，这是一个有着已建立的基准和评估指标的良好定义的问题。
* 用于对象检测的的主要范例有——”Viola＆Jones变体“，HOG + SVM模板，可变形部分检测器（DPM）和卷积神经网络（ConvNets）都已经被探索用于此任务。

## 2 Datasets

* INRIA, ETH, TUD-Brussels, Daimler, Caltech-USA, and KITTI是使用最广的数据集。
* INRIA：INRIA是最古老的，因此具有相对较少的图像。 然而，从不同设置（城市，海滩，山脉等）的行人的高质量注释，这是为什么它被普遍选择用来训练。
* Daimler没有被所有的方法考虑，因为它缺乏颜色通道。
* Daimler stereo，ETH和KITTI提供立体声信息。
* 所有数据集但INRIA都是从视频获取的，因此可以使用光流作为附加提示。
* 今天，Caltech-USA和KITTY是行人检测的主要基准。 两者都相对较大和具有挑战性。

## 3 Main approaches to improve pedestrian detection

* 40+种行人检测的方法：

  ![Snip20161202_22](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfron76nbj30gx0p8gv7.jpg)


* 我们不是讨论方法的个体特性，而是识别区分每种方法（表1的对号）的关键方面，并对其进行分组。 我们在下面的小节讨论这些方面。

### 3.1 Training data

### 3.2 Solution families

* 总体上，我们注意到在40多种方法中，我们可以辨别三个家庭：

1. DPM变体（MultiResC [33]，MT-DPM [39]等）
2. 深度网络（JointDeep [40]，ConvNet [13] ]等）
3. 决策林（ChnFtrs，Roerei等）。

4. 在表1中，我们将这些家族分别识别为DPM，DN和DF。

### 3.3 Better classiﬁers

* 特征和分类器之间的没有明确的界限

### 3.4 Additional data

* 一些方法探索在训练和测试时间利用额外的信息来改进检测。 他们考虑立体图像[45]，光流（使用以前的帧，例如MultiFtr + Motion [22]和ACF + SDt [42]），跟踪[46]或来自其他传感器 。
* 到目前为止，仅基于单个单目图像帧的方法已经能够跟上由附加信息引入的性能改进。

### 3.5 Exploiting context

* 上下文为行人检测提供了一致的改进，虽然改进的规模比额外的测试数据（§3.4）和深层架构（§3.8）要低。 大部分检测质量必须来自其他来源。

### 3.6 Deformable parts

* 对于行人检测，结果是有竞争性的，但不显着。
* 对于行人检测，除了遮挡处理的情况之外，仍然没有关于部件和部件的必要性的明确证据。

### 3.7 Multi-scale models

* 最近已经注意到，不同分辨率的训练不同模型系统地将性能提高1〜2MR百分点
* 尽管不断改进，他们对最终质量的贡献是相当小的。

### 3.8 Deep architectures

* 尽管有共同的叙述，仍然没有明确的证据表明深层网络有利于行人检测的学习功能
* 最成功的方法使用这样的架构来模拟部件，遮挡和上下文的更高级别方面。 获得的结果与DPM和决策林方法相同，使得使用这样涉及的结构的 **优点仍不清楚** 。

### 3.9 Better features

* 特征更多，具有更丰富和更高维的表示，分类任务变得更容易，从而改善结果。
* 越来越多样化的特性已经显示系统地提高性能。
* 尽管通过添加许多渠道的改进，顶级性能检测器仍然达到仅有10个通道：

  * 6个梯度方向，
  * 1个梯度幅度
  * 3个颜色通道

  * 我们命名这些 **HOG + LUV** 。

* 应当注意，还没有更好的用于行人检测的特征可以通过深度学习方法获得。

* 下一个科学的步骤将是开发一个更深刻的理解，什么使好的功能更哈珀，以及如何设计更好的特征。

### 4 Experiments

* 基于我们在上一节中的分析，在对检测质量的影响方面，三个方面似乎是最有希望的：

  * 更好的特征（§3.9
  * 附加数据（§3.4）
  * 上下文信息（§3.5）

### 4.1 Reviewing the eﬀect of features(特征的影响)

* DCT: (discrete cosine transform)离散余弦变换
* 自VJ以来的许多进展可以通过使用基于定向梯度和颜色信息的更好的特征来解释。 对这些众所周知的特征（例如，基于DCT的投影）的简单调整仍然可以产生显着的改进。

### 4.2 Complementarity of approaches

* 在重新审视4.1节中单帧特征的影响之后，我们现在考虑更好的特征（HOG + LUV + DCT），附加数据（通过光流）和上下文（通过人对人的交互）的互补。
* 我们的实验表明，即使从强检测器开始，添加额外的特征，流量和上下文信息在很大程度上是互补的（增加12％，而不是3 + 7 + 5％）。

## 5 Conclusion

* 虽然这些功能中的一些可能是由学习驱动的，但它们主要是通过尝试和错误手工制作的。
* Better features + optical flow + context的结合可以在Caltech-USA上产生最好的检测性能。
* The main challenge ahead seems to develop a deeper understanding of **what makes good features good**, so as to enable the **design of even better ones**.
