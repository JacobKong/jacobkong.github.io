---
layout: post
title: 行人检测论文笔记：How Far are We from Solving Pedestrian Detection?
date: 2016-12-15 15:32:24.000000000 +09:00
---

## 文章疑问点

* Human Baseline 的标准是如何确定的?
* Ground-truth是什么意思？

  * Groun-truth 指的是正确的标注（真实值）
  * 在有监督学习中，数据是有标注的，以(x, t)的形式出现，其中x是输入数据，t是标注.正确的t标注是ground truth，错误的标记则不是。（也有人将所有标注数据都叫做ground truth）。

* Intersection over Union（IoU）是什么？

  * Intersection over Union is an evaluation metric used to measure the accuracy of an object detector on a particular dataset.
  * Any algorithm that provides predicted bounding boxes as output can be evaluated using IoU.
  * As long as we have these two sets of bounding boxes we can apply Intersection over Union.
  * An Intersection over Union score > 0.5 is normally considered a “good” prediction.


* FPPI: False Positive Per Image

* Oracle Experiment: An oracle experiment is used to compare your actual system to how your system would behave if some component of it always did the right thing.

## Abstract

* 调查了当前最先进的方法与“完美单帧检测器”之间的差距。
* 基于Caltech数据集创建了一个人工的基准。
* 手工聚合了顶级检测器经常出现的错误。
* 刻画了定位，前景 vs 背景两方面的错误

  * 针对定位错误：研究了训练集标记噪声对检测器性能的影响
  * 前景 vs 背景错误：研究了convnets，讨论了哪些因素影响其性能

* 提供了一个新的、更纯净的训练/测试标注集。

## 1. Introduction

## 2. Preliminaries

### 2.1 Caltech-USA pedestrian detection benchmark

* 最流行的数据集：Caltech-USA、KITTI

  * Caltech-USA有2.5小时、30Hz的从LA街道的一个check里面录制的
  * 一共350000个标注、覆盖2300各单一的行人
  * 测试集：4024帧

* MR: miss rate

### 2.2 Filtered channel features detector

* 截止到最近的主要会议（CVPR 15），最好的方法是 **Checkerboards**
* Checkerboards：是ICF的一种，ICF(Integral Channels Feature detector)
* 目前最好的执行convnets方法对底层检测建议很敏感，因此我们首先通过优化过滤的通道特征检测器来关注这些建议。
* 环境和光流可以提高检测（额外的提示）

## 3. Analysing the state of the art

### 3.1 Are we reaching saturation?

* 在现在的基准上，我们还有多少提升空间？为了回答这个问题，我们提出可一个人工的基准线作为最低极限。
* 机器检测算法应该达到至少人类水平，最终超过人类水平。
* 人工基准线——为了公平比较，关注于单帧单目检测，注释器需要根据行人外表和单帧环境来注释。
* Intersection over Union (IoU) ≥ 0.5 matching criterion。
* 在所有情况下人类基准线表现远远超过当前最好的检测器，说明对于自动方法来说，还有提升空间。

### 3.2 Failure analysis

3.2.1 Error sources

* 一个检测器可以有两类错误：

  * 假阳性（检测到了背景，或者很弱的定位检测）
  * 假阴性（低得分率或者错过某些行人检测，检测不全）

* FP聚类成11个分类
* FN聚类成6个分类，其中side view 和 cyclists是由于数据集偏差导致的，用这些案例的外部图像增强训练集可能是一个有效的策略。
* 对于small pedestrains，发现低像素是主要困难来源，所以合理的利用所有像素，以及周围上下文是很必要的。

3.2.2 Oracle test cases

* 对于大多数执行最好的方法，localization和background-vs-forground误差对检测质量具有相等的影响。 他们同样重要。

3.3. Improved Caltech-USA annotations

* 原始注释是基于跨越多个帧内插稀疏注释（interpolating sparse annotations ），并且这些稀疏注释不一定位于评估的帧上。
* 我们的目标是两方面：

  * 在一方面，我们希望提供对现有技术的更准确的评估，特别是适合于接近该问题的“最后20％”的评估。
  * 另一方面，我们希望有训练注释，并评估改进的注释导怎么样更好的检测。

* 总之，我们的新注释与人类基线在以下方面不同：训练和测试集都被注释，忽略区域和闭塞也被注释，完整的视频数据用于决策，并且允许同一图像的多个修订。

### 4. Improving the state of the art

4.1. Impact of training annotations

* Pruning benefits:

  * 从原始到修剪注释的主要变化是删除注释错误，从修剪到新的，主要的变化是更好的对齐。
  * 我们在MRN-2中看到，更强的检测器更好地受益于更好的数据，并且检测质量的最大增益来自移除注释错误。

* Alignment benefits:

  * 为了利用新的1×注释来利用9×剩余数据，我们在新的注释上训练模型，并使用该模型在9×部分上重新对准原始注释。
     ![Snip20161204_2](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfsntx4jxj30hn06zwg1.jpg)
  * 因为新的注释更好地对齐，所以我们期望该模型能够修复原始注释中的轻微位置和缩放错误。

  * 结果表明，使用检测器模型来提高整体数据对准确实是有效的，并且更好地对准训练数据导致更好的检测质量（在MRO和MRN中）。

  * 使用高质量注释进行训练可提高整体检测质量，这得益于改进的对齐和减少的注释错误。


4.2. Convnets for pedestrian detection

* AlexNet 和 VGG16都在ImageNet上进行了预先训练，并使用SquaresChnFtrs建议对Caltech 10×（原始注释）进行了微调。

* 可以看出，VGG显着地减少了背景误差，而同时稍微增加了定位误差。

  ![Snip20161204_3](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfsqv4dcdj30df0c7gnt.jpg)

* 虽然卷积在图像分类和一般物体检测中具有很强的结果，但是当在小物体周围产生良好的局部检测分数时，它们似乎有局限性。 边界框回归（和NMS）是当前架构的一个关键因素。

* 表明神经网络的原始分类能力仍有改进的余地。

### 5. Summary

* 相对于human baseline, there is a 10× gap still to be closed.

* 误差特性导致关于如何设计更好的检测器（在3.2节中提及;例如，对于人side-view的数据增加或在垂直轴上延伸检测器接收场）的具体建议。

* 我们通过衡量更好的注释对本地化准确性的影响，以及通过调查使用convnets来改善the background to foreground discrimination，来部分解决了一些问题。我们的研究结果表明，通过适当训练的ICF检测器可以实现显着更好的Alignment，并且，对于行人检测，Convent在localization上能力不强，但是可以通过边界框回归（bounding box regression）部分解决。 对于原始和新注释，所描述的检测方法都能达到最高性能。

  ![Snip20161204_4](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfsrgcdtaj30dh077jue.jpg)

