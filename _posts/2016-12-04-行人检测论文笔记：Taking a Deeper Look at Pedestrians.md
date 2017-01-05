---
layout: post
title: 行人检测论文笔记：Taking a Deeper Look at Pedestrians
date: 2016-12-04 15:32:24.000000000 +09:00
---

## 1. Introduction

### 1.1. Related work

* 第一篇使用convnet进行行人检测的文章：Pedestrian detection with unsupervised multi-stage feature learning.
* DBN-Isol: A different line of work extends a deformable parts model (DPM) [15] with a stack of Restricted Boltzmann Ma- chines (RBMs) trained to reason about parts and occlu- sion (DBN-Isol)
* DBN-Mut: extended to ac- count for person-to-person relations
* JointDeep: jointly optimize all these aspects: optimizes features, parts deformations, occlusions, and person-to-person relations.
* MultiSDP: 网络为每层提供在不同尺度计算的关于行人检测的上下文特征。
* SDN: 使用附加的“可切换层”（RBM变体）来自动学习低级特征和高级部分（例如“头”，“腿”等）。
* DBN-Isol和DBN-Mut利用DPM作为检测方法。
* JointDeep, MultiSDP, and SDN利用HOG+CSS+linear SVM detector作为检测。
* 重要的是要强调ConvNet [37]学习从YUV输入像素预测，而所有其他方法使用额外的手工制作的特征。

  * DBN-Isol and DBN-Mut use HOG features as input.
  * MultiSDP uses HOG+CSS features as input.
  * JointDeep and SDN uses YUV+Gradients as input (and HOG+CSS for the detection proposals).

### 2. Training data

* Caltech
* Caltech validation set
* Caltech10x: we increase the training data tenfold by sampling one out of three frames
* KITTI
* ImageNet, Places

### 3. From decision forests to neural networks
