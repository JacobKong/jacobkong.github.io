---
layout: post
title: 《DeepLearning》读书笔记：DL - Chapter 9 - Conventional Networks
date: 2016-12-01 15:32:24.000000000 +09:00
---

## Chapter 9 Convolutional Networks（卷积神经网络）

* 卷积网络仅仅是在其至少一个层中使用卷积代替一般矩阵乘法的神经网络。

### 9.1 The Convolution Operation

* The convolution operation is typically denoted with an asterisk:

![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfpf9zrgzj307m025t8n.jpg)

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfpdsn8cgj305r01l0sm.jpg)

![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfpdhwyqcj30b5028q2y.jpg)
* 在卷积网络术语中，卷积的第一个参数（在本例中为函数x）通常称为 **输入** ，第二个参数（在本例中为函数w）作为 **内核** 。 **输出** 有时称为 **特征映射(feature map)** 。

* 在机器学习应用中， **输入** 通常是多维数据数组，并且 **内核** 通常是由学习算法调整的多维参数数组。

* 我们将这些多维数组称为 **张量（tensors）** 。
* 这意味着在实践中，我们可以实现无限求和作为对有限数量的数组元素的求和。
* 二维卷积：
  ![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfpdof15bj30ge01yglp.jpg)
  two-dimensional kernel K 卷积是可交换的，这意味着我们可以等价地写，但这样会带来 **kernel-ﬂipping**
  ![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfpdiudboj30gf01wglq.jpg)
  后一种会更更容易用机器学习库来实现。
* 许多神经网络库实现了一个称为互相关（cross-correlation）的相关函数，它与卷积相同，但是没有翻转（flipping）内核：

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfpdwxyypj30fw01vdfy.jpg)

* 卷积的一个例子：

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfpf8xjmyj30oq0lqn0c.jpg)

* 离散卷积可以被看作是乘以矩阵的乘法。
* Toeplitz矩阵：常对角矩阵（又称特普利茨矩阵）是指每条左上至右下的对角线均为常数的矩阵，不论是正方形或长方形的。

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfpdpayklj30c30au74r.jpg)

* 在二维中，双块循环矩阵（doubly block circulant matrix）对应于卷积。

* 卷积通常对应于非常稀疏的矩阵。
* 任何与矩阵乘法一起作用并且不依赖于矩阵结构的特定属性的神经网络算法都应该与卷积一起工作，这样就不需要对神经网络的任何进一步的改变。

### 9.2 Motivation

* 卷积利用三个重要的想法，可以帮助改进机器学习系统:

  * 稀疏的连接（sparse interactions）
  * 参数共享（parameter sharing）
  * 等值表示（equivariant representations）
  * 此外卷积可以处理各种大小输入。

* Sparse Interactions：这是通过使内核小于输入来实现的。

  * 我们需要存储更少的参数，这既减少了模型的内存需求，又提高了其统计效率。
  * 计算输出需要更少的操作。
  * 在深卷积网络中，较深层中的单元可以与输入的较大部分间接交互，This allows the network to eﬃciently describe complicated interactions between many variables by constructing such interactions from simple building blocks that each describe only sparse interactions.
    ![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfpduscrij30ee0eqdho.jpg)
  * Sparse connectivity, viewed from below
    ![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfpdvqhssj30ea0em0ul.jpg)
  * Sparse connectivity, viewed from above
    ![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfpdvqhssj30ea0em0ul.jpg)
  * 在卷积网络的较深层中的单元的接收场大于在浅层中的单元的接收场。这意味着即使卷积网络中的直接连接非常稀疏，更深层中的单元也可以间接地连接到所有或大部分输入图像。

* Parameter sharing：参数共享是指对模型中的多个函数使用相同的参数。

  * 也可以叫 **Tied Weights（捆绑权值），因为应用于一个输入的权重的值与在其他地方应用的权重的值有关。**
  * 在卷积神经网络中，卷积核中的每一个元素都会在input中的每一个位置使用。
  * 在存储器要求和统计效率方面，卷积比密集矩阵乘法显着更有效。

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfpdpjpefj30ee0bzgmu.jpg)
* 黑色箭头表示在卷积模型中3元素核的中心元素的使用。

* Equivariant：说一个函数是等变的意味着如果输入改变，输出以相同的方式改变。

    * 一个函数f(x)与函数g **等变** 如果f(g(x)) = g(f(x)).
    * 当处理时间序列数据时，这意味着卷积产生一种时间线，显示输入中不同特征的出现。
    * This is useful for when we know that some function of a small number of neighboring pixels is useful when applied to multiple input locations.
    * 卷积不是自然地等同于一些其他变换，例如图像的尺度或旋转的变化。

* 卷积是描述在整个输入上应用小的局部区域的相同线性变换的变换的非常有效的方式。

### 9.3 Pooling

* 卷积网络的一个典型层由三个阶段组成:

  * 在第一阶段，该层并行执行几个卷积以产生一组线性激活（linear activation）。
  * 在第二阶段，每个线性激活通过非线性激活函数，例如整流线性激活函数。这一阶段成为 **detector stage**.
  * 在第三阶段，我们使用池化函数（pooing function）来进一步修改层的输出。

![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfpdnsj0rj30k00je76j.jpg)

* pooling function是用附近的汇总统计来替换特定位置的网络的输出。

* 在所有情况下，池化有助于使表示变得对于相对于 **输入的小平移** 几乎不变（invariant）。

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfpdmenf3j30vy0qlwk3.jpg)

* **如果我们更关心某些特征是否存在而不是完全在哪里，那么本地变换的不变性可以是非常有用的属性。**

* 池的使用可以被视为添加一个无限强的先验，层所学习的函数必须对小的变换是不变的。
* 如果我们在单独的参数化的卷积输出上进行赤化，则特征可以学习到对哪一种变换进行不变。
* 利用卷积和pooling的卷积网络架构：

![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfpdrfrclj30jn0orjya.jpg)

### 9.4 Convolution and Pooling as an Inﬁnitely Strong Prior

* 弱先验是具有高熵的先验分布，例如具有高方差的高斯分布。
* 强先验具有非常低的熵，例如具有低方差的高斯分布。这样的先验在确定参数在哪里结束方面起更积极的作用。
* 总的来说，我们可以认为卷积可以用来为一个层的参数引入一个无限强的先验概率分布。
* 同样，池的使用是保证每个单位对小的变换保持不变性的无限强的先验。
* 但是将卷积网视为具有无限强的先验的完全连接的网络可以给我们一些关于卷积网如何工作的见解。

  * 一个关键的见解是卷积和池化可能导致欠拟合。
  * 从这个观点的另一个关键的见解是，我们应该只比较卷积模型与统计学习性能基准中的其他卷积模型。对于许多图像数据集，对于排列不变的模型存在单独的基准，并且必须通过学习发现拓扑的概念，以及具有由他们的设计者硬编码到其中的空间关系的知识的模型。

### 9.5 Variants of the Basic Convolution Function

* 首先，当我们在神经网络的上下文中提到卷积时，我们通常实际上意味着一种由许多卷积应用并行组成的操作。

  * 这是因为单个内核的卷积只能提取一种特征，虽然在许多空间位置。 通常我们希望我们网络的每一层都能在许多位置提取多种特征。

* 此外，输入通常不仅仅是是一个网格的真是数据，反而是矢量值的观测网格。

* 这些多通道操作可交换，仅当每个操作具有与输入通道相同数量的输出通道。
* 我们可能想跳过内核的一些位置，以减少计算成本，我们可以认为这是下采样全卷积函数的输出。
* 如果我们只想对输出中每个方向的每s个像素进行采样，那么我们可以定义下采样卷积函数c：

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfpdkzazkj30dk01jwei.jpg)
* 我们将s称为这个下采样卷积的步幅。 也可以为每个运动方向定义一个单独的步幅。

* 零填充：

  * 任何卷积网络实现的一个基本特征是具备隐含地对输入V进行零填充以便使其更宽的能力。
  * 零填充输入允许我们独立地控制内核宽度和输出的大小。
  * 没有零填充，我们被迫选择快速缩小网络的空间范围并且使用小内核 - 这两个方案，显着地限制了网络的表达力。

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfpdu1l9yj30jk0f376p.jpg)

* 三种zero-padding的情况：

  * valid convolution
  * same convolution
  * full convolution

* Unshared convolution
* Tiled convolution：在卷积层和本地连接层之间提供了折中。我们不是在每个空间位置学习一组独立的权重，而是学习一组内核，从而我们在空间移动时旋转内核。
* 这三个操作做够计算所有训练一个前向卷积网络需要的梯度，以及基于卷积的转置来训练具有重建函数的卷积网络。

  * 卷积
  * 从输出到权值反向传播
  * 从输出到输入反向传播

### 9.6 Structured Outputs

* 卷积网络可以用于输出高维度的结构化对象，而不仅仅是预测分类任务的类标签或回归任务的实际值。
* Pixel labeling：像素标记
* 循环周期卷积网络（recurrent convolutional network）：

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfpfbmq7mj307o07d0t3.jpg)

### 9.7 Data Types

* 与卷积网络一起使用的数据通常由几个通道组成，每个通道是在空间或时间的某个点观察不同的量。

  * 卷积网络的一个优点是它们还可以处理具有变化的空间范围的输入。
  * 有时，网络的输出允许具有 **可变大小以及输入** ，例如如果我们想要为输入的每个像素分配类标签。 **在这种情况下，不需要额外的设计工作了** 。
  * 在其他情况下，网络必须产生一些固定大小的输出，例如，如果我们要为整个图像分配单个类标签。 **在这种情况下，我们必须进行一些额外的设计步骤** ，例如插入一个池化层，其池区域的大小与输入的大小成比例，以 **保持固定数量** 的池化输出。

### 9.8 Eﬃcient Convolution Algorithms

* 现代卷积网络应用通常涉及包含超过一百万个单元的网络。
* **卷积等效于使用傅立叶变换将输入和内核两者转换到频域，执行两个信号的逐点乘法，并使用逆傅里叶变换转换回到时域。**
* 当内核是可分离的，原始的卷积是效率低下的。
* 设计更快的执行卷积或近似卷积的方法，而不损害模型的准确性是一个活跃的研究领域。

### 9.9 Random or Unsupervised Features

* 通常，卷积网络训练中最昂贵的部分是学习特征。
* 降低卷积网络训练成本的一种方式是使用未以受监督方式训练的特征。
* 有三种不需要监督学习来获得卷积内核的策略：

  * 一个是简单地 **随机初始化** 它们。
  * 另一个是用手设计它们，例如通过设置每个内核以在特定方向或尺度检测边缘。
  * 最后，可以使用无监督标准来学习内核。

* 随机滤波器在卷积网络中通常工作得很好
* 一个折中的方法是学习特征，但使用： **每个梯度步骤不需要完全正向和反向传播的** 方法。 与多层感知器一样，我们使用 **贪婪层式预训练** ，独立地训练第一层，然后从第一层提取一次所有特征，然后利用这些特征隔离的训练第二层，等等。
* 不是一次训练整个卷积层，我们可以训练一个小补丁的模型，如用k-means。 然后，我们可以使用来自这个patch-based的模型的参数来定义卷积层的内核。
* 今天，大多数卷积网络以纯粹监督的方式训练，在每次训练迭代中使用通过整个网络的完全正向和反向传播。

### 9.10 The Neuroscientiﬁc Basis for Convolutional Networks

### 9.11 Convolutional Networks and the History of Deep Learning

* 为了处理一维，顺序数据，我们接下来转向神经网络框架的另一个强大的专业化： **循环神经网络（Recurrent neural networks）** 。
