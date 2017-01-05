---
layout: post
title: 行人检测论文笔记：Fused DNN/ A deep neural network fusion approach to fast and robust pedestrian detection
date: 2016-12-05 15:32:24.000000000 +09:00
---

# 论文笔记：Fused DNN: A deep neural network fusion approach to fast and robust pedestrian detection
## 相关知识点

* **L1范数** 也称为最小绝对偏差（LAD），最小绝对误差（LAE）。它基本上最小化目标值(Yi)和估计值(f(xi))之间的绝对差(S)的和

![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfqk8unvtj306d0273ye.jpg)

* L2范数也称为最小二乘。它基本上最小化目标值(Yi)和估计值(f(xi))之间的差(S)的平方的和

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfqk9chp0j305w01wjra.jpg)

## Abstract

* 所提出的网络融合架构允许多个网络的并行处理来提高速度。
* 首先是一个深度卷积网络被训练为一个物体检测器来生成所有有可能的不同尺寸和遮挡的行人候选集。
* 然后，多个深度神经网络被并行使用来之后提炼这些行人候选集。
* 我们引入基于软拒绝的网络融合方法将来自所有网络的软度量融合在一起，以产生最终置信分数。
* 此外，我们提出了一种用于将逐像素语义分割网络（ pixel-wise semantic segmentation network）集成到网络融合架构中作为行人检测器的加强的方法。

## 1. Introduction

* Tradeoff between accuracy and speed.
* 其他因素，如拥挤的场景，非人堵塞物体(non-person occluding objects)或不同的行人外观（不同的姿势或服装风格）也使这个Real-time行人检测问题具有挑战性。
* 行人检测的一般框架可以分解为：
* region proposal generation,
* feature extraction,
* pedestrian verification

* Fused Deep Neural Network(F-DNN)
* 该架构包括行人pedestrian candidiate generator，其通过训练深卷积神经网络获得以，从而具有高检测率，虽然有大的假阳性率。
* 使用深度扩展卷积和上下文聚合的并行语义分割网络[30]为候选行人提供了另一个软的信任投票，它进一步与候选生成器和分类网络融合。

## 2. The Fused Deep Neural Network

* 提出的网络架构包括行人候选生成器，分类网络和像素级语义分割网络。

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfqk6f5o3j30q00iumyv.jpg)

* SSD: a single shot multi-box detector(单镜头多箱检测器)，行人候选生成器是一个single shot multi-box detector（SSD）

* 每个行人候选者与其定位BB坐标和置信度得分相关联。
* 我们提出了一种新的网络融合方法——称为基于软拒绝的网络融合（SNF）。并非是执行接受或拒绝候选者的硬二进制分类，而是基于来自分类器的候选者的 **聚合度** 来提升或折扣行人候选者的置信度分数。
* 我们进一步提出了一种利用具有语义分割（SS）的上下文聚集扩展卷积网络（context aggregation dilated convolutional network with semantic segmentation）作为另一个分类器并将其集成到我们的网络融合架构中的方法。但是在速度上会变得特别慢。

### 2.1. Pedestrian Candidate Generator

* SSD是具有截断VGG16(truncated VGG16)作为基础网络的前馈卷积网络。
* SSD的结构：

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfqk7e7e6j30pe07rgmh.jpg)

* L2归一化技术用于缩小特征量

* 对于大小为m×n×p的每个输出层，在每个位置处设置不同尺度和纵横比的一组默认BB。 将3×3×p个卷积内核应用于每个位置以产生关于默认BB位置的分类分数和BB位置偏移。
* 训练的目标函数是：

![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfqka1k2nj306u01nq2u.jpg)

### 2.2. Classiﬁcation Network and Soft-rejection based DNN Fusion

* 分类网络由多个二元分类深层神经网络组成，这些网络在第一阶段的生成的行人候选集中训练。
* SNF：考虑一个行人候选人和一个分类器。如果分类器对候选人有高的信任度，我们通过乘以大于1的置信因子乘以候选发生器来提高其原始分数。否则，我们以小于1的缩放因子减小其得分。我们将“置信”定义为至少为ac的分类概率。为了融合所有M个分类器，我们将候选者的原始信任得分与分类网络中所有分类器的信任缩放因子的乘积相乘。

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfqkaszbpj30q202qt9i.jpg)

![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfqkaaiiij3086024t8n.jpg)

* SNF背后的关键思想是，我们不直接接受或拒绝任何候选行人，而是基于分类概率的因素来扩展它们。

### 2.3. Pixel-wise semantic segmentation for object detection reinforcement

* 为了执行密集预测，SS网络由完全卷积的VGG16网络组成，其适应于作为前端预测模块的扩展卷积，其输出被馈送到多尺度上下文聚合模块，该多尺度上下文聚合模块由完全卷积网络组成，其卷积层具有增加扩张因子。
* 输入图像被缩放并由SS网络直接处理，SS网络产生具有显示出行人类激活像素的一种颜色和显示出背景的其他颜色的二进遮罩。
* 我们使用以下策略来融合结果：如果行人像素占据候选BB区域的至少20％，我们接受候选者并保持其得分不变; 否则，我们应用SNF来缩放原始的信任分数。

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfqkbeg8wj30g002cwen.jpg)

## 3. Experiments and result analysis

### 3.1. Data and evaluation settings

### 3.2. Training details and results

* **硬拒绝（Hard Rejection）** 被定义为消除由任何分类器分类为假阳性的任何候选者。
