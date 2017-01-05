---
layout: post
title: 深度学习论文笔记：Rich feature hierarchies for accurate object detection and semantic segmentation
date: 2016-12-06 15:32:24.000000000 +09:00
---

## Abstract

* mAP: mean average precision，平均准确度
* 我们的方法结合两个关键的见解：

  * 第一：采用高容量的卷积神经网络来从上到下的进行region proposal，从而实现定位和分割物体。
  * 当标记的训练数据稀缺时，可以先对辅助数据集（任务）进行受监督的预训练， 随后是基于域进行特定调整，产生显着的性能提升。

## 1. Introduction

* 关于各种视觉识别任务的上一个十年的进展主要基于SIFT和HOG的使用
* 实现这个结果需要解决两个问题：

  * 利用深度网络将对象定位
  * 仅利用少量的注释检测数据来训练训练高容量模型。

* 我们通过在“使用区域识别”范例内操作，来解决CNN定位问题
* 在测试时，我们的方法为输入图像生成大约2000个类别无关区域提案，使用CNN从每个proposal中提取固定长度的特征向量，然后使用类别特定的线性SVM对每个区域进行分类。
* 检测中面临的第二个挑战是标记的数据不足，目前可用的数据数量不足以训练大型CNN。这个问题的常规解决方案是使用无监督预训练，然后是监督 fine-tuning。
* 我们发现，对于CNN，有很大比例的参数（94%）可以在检测精度的适度降低的情况下被去除。
* 我们证明一个简单的 **边界框回归方法（bounding box regression）** 显着减少误定位，这是主要的误差模式(error mode)。
* 在开发技术细节之前，我们注意到，因为R-CNN在是区域上操作，所以很自然将其扩展到语义分割（semantic segmentation）的任务。

## 2. Object detection with R-CNN

* 我们的对象检测系统由三个模块组成:

  * 首先生成类别独立(category-independent)区域proposal。 这些proposal定义了可用于检测器的候选检测集合。
  * 第二个模块是大卷积神经网络，从每个区域提取固定长度的特征向量。
  * 第三个模块是一类特定类型的线性SVM。

### 2.1. Module design

* Region proposals: 目前有很多用来生成category-independent的region proposal的方法：

  * Objectness
  * selective search
  * category-independent object proposals
  * constrained parametric min-cuts (CPMC)
  * multi-scale combinatorial grouping
  * detect mitotic cells by applying a CNN to regularly-spaced square crops, which are a special case of region proposals.(通过将CNN应用于规则间隔的方形作物来检测有丝分裂细胞，这是区域提案的特殊情况。)

* 虽然R-CNN与特定区域建议方法无关，但我们使用选择性搜索(selective search)来实现与先前检测工作的受控比较

* Feature extraction:我们从每个区域提案中提取一个4096维特征向量，特征通过前向传播对227×227 RGB图像通过 **五个卷积层和两个完全连接的层** 计算。
* 无论候选区域的大小或宽高比如何，我们都会将其周围的紧密边界框中的所有像素装到所需的大小(227x227像素尺寸)。

### 2.2. Test-time detection

* 在测试时，我们对测试图像运行选择性搜索以提取大约2000个区域建议（我们在所有实验中使用选择性搜索的“快速模式（fast mode）”）。
* 给定图像中的所有得分区域，我们应用贪心非最大抑制(greedy non-maximum suppression)（对于每个类独立地），如果与的饭较高的区域有重叠，且IoU大于学习到的阈值，则该拒绝区域。
* Run-time analysis.两个属性使检测更高校。

  * 首先，所有CNN参数在所有类别中共享。
  * 第二，CNN计算的特征向量与其他常见方法（例如具有视觉词袋编码的空间棱金字塔）相比是 **低维的** 。
  * 唯一的类特定(class-specific)计算是特征和SVM权重之间的点积和非最大抑制。

### 2.3. Training

* 除了用随机初始化的21路分类层（对于20个VOC类加上背景）替换CNN的ImageNet特定的1000路分类层之外，CNN架构是不变的。
* 我们将所有region proposal与一个ground-truth重叠为IoU>0.5，作为该框类的阳性，其余作为阴性。
* 我们以0.001的学习速率（初始预训练速率的1/10）开始SGD，这允许精细调整进行，而不是破坏初始化。
* 一旦提取特征并应用训练标签，我们对每个类优化一个线性SVM。
* 由于训练数据太大，无法记忆，我们采用标准 **hard negative mining method** 。

### 2.4. Results on PASCAL VOC 2010-12

## 3. Visualization, ablation, and modes of error

### 3.1. Visualizing learned features

* pool-5，是网络第五个也是最后一个卷基层的max-pool层的输出。（是一个max-pooling层）
* The pool-5 feature map is 6 × 6 × 256 = 9216维。
* 忽略边界效应，每个pool-5单元在原始227×227像素输入中具有195×195像素的接收场。

### 3.2. Ablation studies

* Fc6与pool-5全连接，为了计算特征，他它将 **4096×9216的权重矩阵乘以pool-5的feature map** （重新形成为9216维矢量），然后添加偏差矢量。
* Fc7是网络的最后一层，通过将由fc 6计算的特征乘以 **4096×4096** 权重矩阵，并类似地添加偏置矢量和应用半波整流来实现。
* 大多数CNN的表示能力来自它的卷积层，而不是来自大得多的密集连接的层。
* All R-CNN variants strongly outperform the three DPM baselines

### 3.3. Detection error analysis

### 3.4. Bounding box regression

## 4. Semantic segmentation

* full
* fg
* full+fg
* The fg strategy slightly outperforms full, indicating that the masked region shape provides a stronger signal, matching our intuition.

## 5. Conclusion

* 之前最好的性能系统是将多个低级图像特征与来自对象检测器和场景分类器的高级上下文组合在一起的复杂集合。
* 本文提出了一个简单和可扩展的对象检测算法，与PASCAL VOC 2012上的最佳以前的结果相比提供30％的相对改进。
* 我们推测“supervised pre-training/domain-speciﬁc ﬁne-tuning”范例将对各种数据缺乏的视觉问题高度有效。
