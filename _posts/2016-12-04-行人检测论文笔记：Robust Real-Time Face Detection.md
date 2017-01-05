---
layout: post
title: 行人检测论文笔记：Robust Real-Time Face Detection
date: 2016-12-15 15:32:24.000000000 +09:00
---

## 知识点

* 傅里叶变换的一个推论：
  ![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfrudkda8j308h01d3yg.jpg)

一个时域下的复杂信号函数可以分解成多个简单信号函数的和，然后对各个子信号函数做傅里叶变换并再次求和，就求出了原信号的傅里叶变换。

* 卷积定理(Convolution Theorem)：信号f和信号g的卷积的傅里叶变换，等于f、g各自的傅里叶变换的积
  ![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfrue2zlyj304n01gdfp.jpg)

![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfrufxyw0j306z0263yf.jpg)

![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfru90t5uj30jt09j75c.jpg)

整个过程的核心就是“（反转），移动，乘积，求和”

* 二维卷积

* 数学定义
  ![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfru5ml16j30f901kdfv.jpg)

二维卷积在图像处理中会经常遇到，图像处理中用到的大多是二维卷积的离散形式：
![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfrum5q4ej30cv01o74a.jpg)

```
* 图像处理中的二维卷积，二维卷积就是一维卷积的扩展，原理差不多。核心还是（反转），移动，乘积，求和。这里二维的反转就是将卷积核沿反对角线翻转，比如：
```

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfru4uj25j307m02wjrd.jpg)

之后，卷积核在二维平面上平移，并且卷积核的每个元素与被卷积图像对应位置相乘，再求和。通过卷积核的不断移动，我们就有了一个新的图像， **这个图像完全由卷积核在各个位置时的乘积求和的结果组成。**

* 巴拿赫空间：更精确地说，巴拿赫空间是一个具有范数并对此范数完备的向量空间。
* 许多在数学分析中学到的无限维函数空间都是巴拿赫空间。
* 巴拿赫空间有两种常见的类型：“实巴拿赫空间”及“复巴拿赫空间”，分别是指将巴拿赫空间的向量空间定义于由实数或复数组成的域之上。

* Overcomplete：
* 对于Banach space X中的一个子集，如果X中的每一个元素都可以利用子集中的元素在范数内进行有限线性组合来良好近似，则该系统X是完备Complete的。
* 该完备系统是过完备（Overcomplete）的，如果从子集中移去一个元素，该系统依旧是完备的，则该系统称为过完备的。
* 在不同的研究中，比如信号处理和功能近似，过完备可以帮助研究人员达到一个更稳定、更健壮，或者相比于使用基向量更紧凑的分解。
* 如果 # (basis vector基向量)>输入的维度，则我们有一个overcomplete representation.

* ROC曲线：在信号检测理论中，接收者操作特征曲线（receiver operating characteristic curve，或者叫ROC曲线）是一种坐标图式的分析工具，用于
* (1) 选择最佳的信号侦测模型、舍弃次佳的模型。
* (2) 在同一模型中设定最佳阈值。
* 从 (0, 0) 到 (1,1) 的对角线将ROC空间划分为左上／右下两个区域，在这条线的 **以上的点** 代表了一个 **好** 的分类结果（胜过随机分类），而在这条线 **以下的点** 代表了 **差** 的分类结果（劣于随机分类）。
* 完美的预测是一个在左上角的点.

* 曲线下面积（AUC）：ROC曲线下方的面积，若随机抽取一个阳性样本和一个阴性样本，分类器正确判断阳性样本的值高于阴性样本之机率=AUC。简单说：AUC值越大的分类器，正确率越高。

## Abstract

* 介绍一个脸部检测框架。
* 三个贡献：
* 引入新图像表示——称为“积分图像”，其允许我们的检测器非常快速地计算所使用的特征。
* 提出一个利用AdaBost学习算法构建的简单有效的分类器，来从极大潜在特征集中选出很少的关键视觉特征。
* 在级联中组合分类器，从而快速丢弃图像的背景区域，同时在有可能的面部区域上花费更多的计算。

## 1. Introduction

* Haar Basis 函数：
  ![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfru9jaxij308q05st8t.jpg)

![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfrucbw2pj30fn07kaaj.jpg)

* Integral image: 类似于计算机图形学中利用求和区域表来进行纹理映射。

* Haar-like features：就是mount两个或多个区域的像素值之和的差值。
* AdaBoost：自适应增强， 具体说来，整个Adaboost 迭代算法就3步：
* 初始化训练数据的权值分布。如果有N个样本，则每一个训练样本最开始时都被赋予相同的权值：1/N。
* 训练弱分类器。具体训练过程中，如果某个样本点已经被准确地分类，那么在构造下一个训练集中，它的权值就被降低；相反，如果某个样本点没有被准确地分类，那么它的权值就得到提高。然后，权值更新过的样本集被用于训练下一个分类器，整个训练过程如此迭代地进行下去。
* 将各个训练得到的弱分类器组合成强分类器。各个弱分类器的训练过程结束后，加大分类误差率小的弱分类器的权重，使其在最终的分类函数中起着较大的决定作用，而降低分类误差率大的弱分类器的权重，使其在最终的分类函数中起着较小的决定作用。换言之，误差率低的弱分类器在最终分类器中占的权重较大，否则较小。

* 级联检测过程的结构基本上是简并决策树的结构

## 2. Features

* 基于特征的系统操作肯定比一个基于像素的系统更更快
* （Two-rectangle feature）两矩形特征的值是两个矩形区域内的像素之和的差
* (Three-rectangle feature)三矩形特征计算从中心矩形中的和减去的两个外部矩形的和。
* (Four-rectangle feature)四矩形特征计算矩形对角线对之间的差异。
  ![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfrul9r26j30aw09ydg6.jpg)

矩阵特征=从灰色矩形中的像素的和中减去位于白色矩形内的像素的和。

### 2.1 Integral Image

* 矩阵特征可以通过图像的中间表示来快速计算，从而成为Integral Image.

* 积分图的每一点（x, y）的值是原图中对应位置的左上角区域的所有值得和。

* 积分图每一点的（x, y）值是：
  ![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfrujq2ygj30f601g749.jpg)

* 位置x，y处的积分图像包含x，y（包括端点）上方和左侧的像素的和:
  ![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfruhirv4j309d021t8p.jpg)

ii(x, y) is the integral image

i(x, y) is the original image
![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfru76drdj30au02gwem.jpg)

s（x，y）是累积行和

s(x, −1) = 0, ii(−1, y) = 0)

积分图可以只遍历一次图像即可有效的计算出来

* 使用积分图像，可以在四个阵列参考中计算任何矩形和。
  ![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfru8nyo9j30c8096jrh.jpg)

![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfruav2urj30d602jwej.jpg)

* Two-rectangle feature：需要6个阵列参考
  ![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfrughx0aj30ga0gr408.jpg)

* Three-rectangle feature：需要8个阵列参考

* Four-rectangle feature：需要9个阵列参考
* 在线性运算（例如f.g）的情况下，如果其逆被应用于结果，则任何可逆线性算子可以应用于f或g。

* 例如在卷积的情况下，如果导数运算符被应用于图像和卷积核，则结果必须被双重积分.
  ![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfru56d7dj307g01ra9z.jpg)

* 如果f和g的导数稀疏（或可以这样做），卷积可以显着加速。

* 类似的一个认识是：如果其逆被应用于g，则一个可逆线性算子可以应用于f。
  ![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfru68n1lj307x01tdfs.jpg)

* 在该框架中观察，矩形和的计算可以表示为点积i·r，其中i是图像，r是box car图像（在感兴趣的矩形内的值为1，外面是0）。 此操作可以重写：
  ![](https://ww2.sinaimg.cn/large/006tKfTcgw1fbfrufk96vj306c02gwee.jpg)

积分图像实际上是图像的二重积分（首先沿行，然后沿列）。

* 矩形的二阶导数（第一行在行中，然后在列中）在矩形的角处产生四个delta函数。 第二点积的评估通过四个阵列访问来完成。

### 2.2. Feature Discussion

* 与可操纵滤波器（Steerable filters）等替代方案相比，矩形特性有点原始。
* 可控滤波器对边界的详细分析，图像压缩和纹理分析的非常有用。
* 由于正交性不是这个特征集的中心，我们选择生成一个非常大而且各种各样的矩形特征集。
* 从经验上看，似乎矩形特征集提供了丰富的图像表示，能支持有效的学习。
* 为了利用积分图像技术的计算有事，考虑用更常规的方法去计算图像金字塔。
* 像大多数面部检测系统一样，我们的检测器在许多尺度扫描输入; 从以尺寸为24×24像素检测面部的基本刻度开始，在12个刻度以大于上一个的1.25倍的因子扫描384×288像素的图像。

## 3. Learning Classification Functions

* 给定检测器的基本分辨率是24×24，矩形特征的穷尽集是相当大的，160000.
* 我们的假设是，由实验证明，非常少数的矩形特征可以组合形成一个有效的分类器。 **主要的挑战是找到这些功能。**
* Adaboost：将多个弱分类器组合成一个强分类器。（一个简单学习算法叫做weak learner）。
* 传统的的AdaBoost过程可以容易地解释为贪心特征选择过程。
* 一个 **挑战** 是将大的权重与每个良好的分类函数相关联，并将较小的权重与较差的函数相关联。
* AdaBoost是一个用于搜索少数具有显着品种的良好“特征”的有效程序。
* 将一个weak learn限制到分类函数几何中，每一个函数都只依赖于一个单一的特征。
* 若学习宣发选择单一的能够最好分开正和负样本的矩形特征。
* 对于每一个特征，weak learner决定最优分类函数阈值，从而可以使得最少数目的样本被错分。
* 一个弱分类器h(x, f, p, θ)因此包含一个特征f，一个阈值θ，一个显示不等式方向的极性p：
  ![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfruf310ij30ai01odfv.jpg)

这里x是一个图片24*24像素的子窗口。

* 我们使用的弱分类器（阈值单一特征）可以被视为单节点决策树。
* **Boosting 算法** ：T是利用每个单个特征构造的假设，最终假设是T个假设的加权线性组合，其中权重与训练误差成反比。

1. 给定样本图片(x1, y1), (x2, y2), ..., (xn, yn)。其中yi=0, 1分别为负样本和正样本。
2. 初始化权值w1, i=1/(2m), 1/(2l)分别当yi=0, 1。其中m和l分别是负样本和正样本的数量。
3. For t=1, ..., T:

4. 归一化权重,
   ![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfrucsnrpj305e01bgli.jpg)

5. 根据加权错误选择最佳弱分类器：
   ![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfruidyr6j30cx01zgln.jpg)

6. 定义 ht(x) = h(x, ft, pt,θt) 其中ft, pt, 和 θt 是εt的最小值.

7. 更新权值：
   ![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfruadstnj306101c0sm.jpg)

其中ei=0当样例xi被正确的分类，否则ei=1，并且
![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfruklba8j303c017mx0.jpg)

```
4. 最后的强分类器是：
```

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfru83b9xj30c104xjrk.jpg)

其中
![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfru6hdsij303t0160sl.jpg)
### 3.1. Learning Discussion

* 弱分类器选择算法过程如下：

* 对于每个特征，根据特征值对样例进行排序。
* 该特征的AdaBoost最佳阈值可以在该排序列表上的单次通过中计算。
* 对于排序列表中的每个元素，四个和被维护和评估：
* 正实例权重T+的总和。
* 负实例权重T-的总和。
* 当前示例S+之下的正权重的和。
* 当前示例S-之下的负权重的和。

* 在排序一个划分当前和上一示例之间的范围的阈值的错误是：
  ![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfrud40xsj30dk01gjrd.jpg)

### 3.2. Learning Results

* 在现实应用中，假正例率必须接近1/1000000。
* 所选择的 **第一特征** 似乎集中于属性即眼睛的区域通常比鼻子和脸颊的区域更暗。
* 所选择的 **第二特征** 依赖于眼睛比鼻梁更暗的特性。
* 提高性能最直接技术是添加更多的特征，但这样直接导致计算时间的增加。
* Receiver operating characteristic (ROC)曲线：
  ![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfruhvzrpj30e30ao3yq.jpg)

## 4. The Attentional Cascade

* 本节描述了用于构造级联的分类器的算法，其实现了提高的检测性能，同时从根本上减少了计算时间。
* 阈值越低，检测率越高，假正例率越高。
* 从双特征强分类器开始，可以通过 **调整强分类器阈值** 以最小化假阴性来获得有效的面部滤波器。
* 可以调整双特征分类器以50％的假阳性率来检测100％的面部。
* 整体的检测过程形式是简并决策树的形式，我们称之为“级联”。
* 在任何点上的否定结果立即导致对该子窗口的拒绝。
* 更深的分类器面临的更困难的例子,将整个ROC曲线向下推。 在给定的检测率下，较深的分类器具有相应较高的假阳性率。

### 4.1. Training a Cascade of Classifiers

* Given a trained cascade of classifiers, the false positive rate of the cascade is：

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfru7u7f1j303g02bjr9.jpg)

* The detection rate is:

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfrumlwqej303p02idfp.jpg)

* The expected number of features which are evaluated is:

![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfrubq3n2j307s02c0sq.jpg)

* 用于训练后续层的负样例集合是通过运行检测器收集通过在不包含任何面部实例的一组图像上而找到的所有错误检测来获得。

* 构建一个练级检测器的训练算法：

### 4.2. Simple Experiment

### 4.3. Detector Cascade Discussion

* 将检测器训练为分类器序列的隐藏好处是:最终检测器看到的有效数目的负样例数目可能非常大。
* 在实践中，由于我们的检测器的形式和它使用的特性是非常高效的，所以在每个尺度和位置评估我们的检测器的 **摊销成本** 比在整个图像中找到并分组边缘更快。

## 5. Results

### 5.1. Training Dataset

* 事实上，包含在较大子窗口中的附加信息可以用于在检测级联中较早地拒绝non-face。

### 5.2. Structure of the Detector Cascade

* 最终的检测器是38层分级器，包括总共6060个特征。
* 级联中的第一个分类器是使用两个特征构造的，在检测100%的面部时可以拒绝50%的non-faces.
* 下一个分类器具有十个特征，并且在检测几乎100％的面部时拒绝80％的非面部。
* 接下来的两层是25个特征分类器，其后是三个50特征分类器，再之后是具有根据表2中的算法选择的各种不同数目的特征的分类器。
* 添加更多层，直到验证集上的假阳性率接近零，同时仍保持高的正确检测率。

### 5.3. Speed of the Final Detector

* 级联检测器的速度直接与每个被扫描的子窗口的特征数量相关。

### 5.4. Image Processing

* 用于训练的所有示例子窗口被 **方差归一** 化以使不同照明条件的影响最小化。
* 可以使用 **一对积分图像** 来快速计算图像子窗口的方差。
* 在扫描期间，可以通过对特征值进行后乘，而不是对像素进行操作来实现图像归一化的效果。

#行人检测 #检测算法 