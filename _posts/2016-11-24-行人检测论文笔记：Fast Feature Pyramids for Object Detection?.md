---
layout: post
title: 行人检测论文笔记：Fast Feature Pyramids for Object Detection?
date: 2016-11-24 15:32:24.000000000 +09:00
---

## 相关知识点

* Overcomplete Representations:

  * Overcomplete：Such a complete system is overcomplete if removal of a \phi _{j} from the system results in a system (i.e., {\phi _{i}}_((i\in J\backslash {j))}) that is still complete.
  * In different research, such as signal processing and function approximation, overcompleteness can help researchers to achieve a more stable, more robust, or more compact decomposition than using a basis.[2]

* Image pyramid：影响金字塔

  * 影像金字塔由原始影像按一定规则生成的由细到粗不同分辨率的影像集。
  * 指在同一的空间参照下，根据用户需要以不同分辨率进行存储与显示，形成分辨率由粗到细、数据量由小到大的金字塔结构。
  * 图像编码和渐进式图像传输
  * 从图中可以看出, 从金字塔的底层开始每四个相邻的像素经过重采样生成一个新的像素, 依此重复进行, 直到金字塔的顶层。重采样的方法一般有以下三种: 双线性插值、最临近像元法、三次卷积法。
  * 金字塔是一种能对栅格影像按逐级降低分辨率的拷贝方式存储的方法。通过选择一个与显示区域相似的分辨率，只需进行少量的查询和少量的计算，从而减少显示时间。

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfr9u09c3j305r064dfv.jpg)

* Gradient Histograms:

## Abstract

* The computational bottleneck of many modern detectors is the **computation of features** at every scale of a finely-sampled image pyramid.
* The approach is general and is widely applicable to vision algorithms requiring fine-grained multi-scale analysis.

## 1. Introduction

* Multi-orientation decompostion: 多向分解，是图像处理中的基本技术。
* 在每个尺度和方向分别分析图像结构的想法起源于多个来源:

  * 哺乳动物视觉系统生理学的测量
  * 有关视觉信息的统计和编码的原则性推理
  * 谐波分析
  * 谐波分析（多速率滤波)

* 灵长类视觉系统显示：条纹皮层细胞（粗略的等价于图像的小波展开）在数量上超过视网膜神经节细胞（图像像素的近似表示）10^2到10^3.
* 这些表示相对于视点，照明和图像变形的变化的鲁棒性是Overcomplete Representations 优越性能的促成因素。
* 不幸的是，更高的检测正确率通常伴随着更高的计算开销。
* 在计算开销和为了提高检测和降低错误率而使用更复杂的表示之间是没有必要做权衡的。
* 自然图像具有分形统计，使得我们可以利用这一点来更可靠的预测图像跨尺度结构。
* 我们证明了我们提出的快速特征金字塔与三个不同的检测框架的有效性：

  * 积分通道特征（ICF）
  * 聚集通道特征（积分通道特征的新颖变体）
  * 可变形零件模型（DPM）

## 2. Related Work

* Scale Space Theory: 尺度空间理论
* Cascades, coarse-to-fine search, distance transforms, etc., 全部都关注于对提前计算好的图像特征来优化分类速度。
* 本方法专注于快速特征金字塔的构建，因此与上面的方法可以起到互补的效果。
* 行人检测的最佳执行方法[31]和PASCAL VOC [38]是基于多尺度特征金字塔的滑动窗[21]，[29]，[35]; 快速特征金字塔非常适合于这种滑动窗口检测器。

## 3. Multiscale Gradient Histograms
