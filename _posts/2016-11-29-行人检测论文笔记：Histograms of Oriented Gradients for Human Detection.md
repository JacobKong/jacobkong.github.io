---
layout: post
title: 行人检测论文笔记：Histograms of Oriented Gradients for Human Detection
date: 2016-11-29 15:32:24.000000000 +09:00
---

## 相关知识点

* 从TP、FP、TN、FN到ROC曲线、miss rate

  * TP：true positive，实际是正例，预测为正例
  * FP：false positive，实际为负例，预测为正例
  * TN：true negative，实际为负例，预测为负例
  * FN：false negative，实际为正例，预测为负例

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfrffyhsdj30eu03mjrv.jpg)

* fnr+tpr=1, fpr+tnr=1
* miss rate = FNR = 1 - true positive
    * 对于一个确定的阈值t，FPR和TPR是确定的，得到一个(fpr,tpr)元组。
    * 当t增加， # FP也减小， # TN增加，则fpr减小；
    * 当t增加， # TP减小， # FN增加，则tpr减小。
    * 也就是说，当阈值t从0变化到1，fpr和tpr也单调减小，从(1，1)减小到(0,0)
    * miss rate = 1 - true positive rate，那么对应的YoX图像，也就是miss rate - false positive rate图像，就应当是单调下降的曲线。

## Abstract

* 定向梯度直方图（HOG）描述符的网格显著优于现有的人体检测特征集。
* 在重叠描述符块中的 **精细尺度梯度** ， **精细定向分箱** ， **相对粗略的空间分箱** 和 **高质量局部对比度标准化** 对于良好的结果都是重要的。
* 新的数据集

## 1 Introduction

* 第一需求：robust feature set.
* 我们研究了人类监测的特征集问题，发现 **本地归一化的定向梯度直方图（HOG）** 描述符提供优异的性能相对于其他现有特征集包括小波。
* 提出的描述符让人联想到 **边缘方向直方图** [4,5]， **SIFT描述符** [12]和 **形状上下文** [1]，但与它们的不同点是：HOG描述器是在一个网格密集的大小统一的细胞单元（dense grid of uniformly spaced cells）上计算，而且为了提高性能，还采用了重叠的局部对比度归一化（overlapping local contrast normalization）技术。

## 2 Previous Work

## 3 Overview of the Method

* The method is based on evaluating well-normalized local histograms of image gradient orientations in a dense grid.
* 基本思想是，在一副图像中，局部目标的 **表象** 和 **形状** （appearance and shape）能够被梯度或边缘的方向密度分布很好地描述，即使没有对应的梯度或边缘位置的精确知识。
* 具体的实现方法是：首先将图像分成小的连通区域，我们把它叫细胞单元（cell）。然后采集细胞单元中各像素点的梯度的或边缘的方向直方图。最后把这些直方图组合起来就可以构成特征描述器。
* 为了提高性能，我们还可以把这些局部直方图在图像的更大的范围内（我们把它叫区间或block）进行对比度归一化（contrast-normalized），所采用的方法是：先计算各直方图在这个区间（block）中的密度，然后根据这个密度对区间中的各个细胞单元做归一化。 **通过这个归一化后，能对光照变化和阴影获得更好的效果。**
* 整体的物体检测链：

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfrfi9crxj30ww05udiy.jpg)

* 这些基于稀疏特征的表示的成功有点遮蔽了HOG作为密集图像描述符的能力和简单性。

* HOG/SIFT表示方法有几个优点。

  * 由于HOG方法是在图像的局部细胞单元上操作，所以它对图像几何的（geometric）和光学的（photometric）形变都能保持很好的不变性，这两种形变只会出现在更大的空间领域上。
  * 他捕捉了局部形状非常具有特征性的边和梯度特征。
  * 在局部表示中对局部的几何和光度变换的不变性更容易控制。
  * 如果它们远小于局部空间或方向仓尺寸，则平移或旋转几乎没有差别。

* 作者通过实验发现，在粗的空域抽样（coarse spatial sampling）、精细的方向抽样（fine orientation sampling）以及较强的局部光学归一化（strong local photometric normalization）等条件下，只要行人大体上能够保持直立的姿势，就容许行人有一些细微的肢体动作，这些细微的动作可以被忽略而不影响检测效果。综上所述，HOG方法是特别适合于做图像中的行人检测的。

## 4 Data Sets and Methodology

* hard examples
* Detection Error Tradeoff (DET) curves on a log-log scale. miss rate(1-Recall / FN/(TP+FN)) verses FPPW. 值越低越好。
* DET图和ROC图提供的信息一眼，但是前者允许小概率更容易的去分布。
* FPPW：NUMBER_OF_FALSE_POSITIVE/NUMBER_OF_WINDOWS
* 我们的DET曲线通常相当浅，所以即使非常小的缺失率的改善也等同于在不变缺失率下的情况下FPPW中的大增益。

## 5 Overview of Results

* Generalized Haar Wavelets.
* PCA-SIFT.
* Shape Contexts.

## 6 Implementation and Performance Study

* 默认检测器：

  * RGB colour space with no gamma correction
  * [−1, 0, 1] gradient filter with no smoothing
  * linear gradient voting into 9 orientation bins in 0◦ –180◦
  * 16×16 pixel blocks of four 8×8 pixel cells
  * Gaussian spatial win- dow with σ = 8 pixel
  * L2-Hys (Lowe-style clipped L2 norm) block normalization
  * block spacing stride of 8 pixels (hence 4-fold coverage of each cell)
  * 64×128 detection window;
  * linear SVM classifier.

* 主要的结论是，为了良好的性能，应该使用细尺度导数（基本上没有平滑），许多定向仓,中等大小，强归一化，重叠的描述符块。

### 6.1 Gamma/Colour Normalization

### 6.2 Gradient Computation

* 最通常用的方法就是简单的应用一个一维的离散的梯度模版分别应用在水平和垂直方向上去。可以使用如下的卷积核进行卷积：

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfrfj1a8sj3066017q2t.jpg)

### 6.3 Spatial / Orientation Binning(方向单元划分)

* 每个块内的每个像素对 **方向直方图** 进行投票
* 每个像素基于以其为中心的梯度元素的方向计算边缘取向直方图通道的 加权投票，并且投票被累积到在称为 **单元** 的局部空间区域上的 **方向仓** 中。
* 每个块的形状可以是矩形或圆形的
* 方向直方图的方向取值可以是0-180度或者0-360度，这取决于梯度是否有符号。无符号梯度（0-180º），有符号梯度（0-360º）
* 为了减少混叠，投票在相邻仓中心之间以取向和位置双向内插。
* 至于投票的权重，可以是梯度的幅度本身或者是它的函数。投票是像素处的梯度幅度的函数，或者是幅度本身、其平方、其平方根或者表示像素的边缘的软出现/缺失的幅度的限幅形式。在定向编码对于良好的性能是至关重要的。
* 梯度幅度本身通常产生最好的结果。其它可选的方案是采用幅度的平方或开方，或者幅度的裁剪版本。
* Dalal和Triggs发现在人的检测实验中，把方向分为 **9个通道(bin)** 效果最好

### 6.4 Normalization and Descriptor Blocks

* 由于照明的局部变化和前景-背景对比度，梯度强度在可以在很宽范围内变化。所以梯度强度必须要局部地归一化，这需要把方格(cells)集结成更大、在空间上连结的区()
* **有效的局部对比度归一化** 对于良好的性能是必不可少的。
* 我们评估了多种不同的 **归一化schemes(normalization schemes)** ，他们大多数都是基于将单元格（cells）分组成更大的空间块（spatial blocks）*  *并且对比地单独对每个块进行归一化。
* **最终描述符** 是来自检测窗口中的所有块的归一化单元响应的所有分量的 **向量** 。
* R-HOG：R-HOG块和SIFT描述符有许多相似之处，但是他们用途十分不同。

  * R-HOG跟SIFT描述器看起来很相似，但他们的不同之处是：

    * R-HOG是在单一尺度下、密集的网格内、没有对方向排序的情况下被计算出来；
    * 而SIFT描述器是在多尺度下、稀疏的图像关键点上、对方向排序的情况下被计算出来。
    * R-HOG是各区间被组合起来用于对空域信息进行编码，而SIFT的各描述器是单独使用的。

* 它们在密集网格中以单个尺度计算而没有主要取向对准，并且用作隐式地去编码相对于检测窗口的空间位置的较大代码矢量的一部分，而SIFT在稀疏集合的 **尺度不变关键点处** 被计算，旋转以对准它们的主导方向，并单独使用。
* SIFT被优化用于稀疏宽基线匹配，R-HOG用于空间形式的密集鲁棒编码。
* R-HOG区块一般来说是多个方格子组成的，由三个参数表示：

  * 每个区块(block)有多少方格(cell)、
  * 每个方格(cell)有几个像素(pixel)、
  * 每个方格(cell)直方图有多少频道(bin)。

* 对于人体检测，3x3的单元块，6x6的像素单元块儿表现最好，同时直方图是9通道。

![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfrfhlpopj30ck095gmg.jpg)

* 当其太小（1×1单元块，即，单独取向上的归一化）时，有价值的空间信息被抑制。

* 在对直方图做处理之前，给每个区间加一个高斯空域窗口是非常必要的，因为这样可以降低边缘的周围像素点的权重。

* C-HOG

  * 每个空间单元包含梯度加权取向单元的堆叠而不是单个取向无关的边缘计数。
  * 对数极坐标网格最初是由允许附近结构的精细编码与较宽上下文的粗略编码相结合的思想，以及从灵长类动物的 **视野** 到 **V1皮层** 的变换是 **对数的**
  * 然而，具有非常少的径向箱的小描述符反而能给出最好的性能，因此在实践中 **几乎没有不均匀性或上下文** 。
  * 我们评估了C-HOG几何的两个变体，一个具有 **单个圆形中心细胞** （类似于[14]的GLOH特征），以及中心细胞被分成 **角形扇区的形状上下文** 。

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfrfkf2vgj302604a0sn.jpg)

*  C-HOG的4个参数：

   *  the numbers of angular(角度盒子的个数）；

   *  the numbers of radias(半径盒子个数)
   *  the radius of the central bin in pixels（中心仓的半径（以像素为单位））
   *  the expansion factor for subsequent (半径的伸展因子）

*  为了良好的性能，最佳的参数设置为：4个角度盒子、2个半径盒子、中心盒子半径为4个像素、伸展因子为2

*  4像素是中央bin的最佳半径，但3和5给出类似的结果。

*  C-HOG看起来很像基于形状上下文（英语：Shape context）的方法，但不同之处是：C-HOG的区间中包含的细胞单元有多个方向通道，而基于形状上下文的方法仅仅只用到了一个单一的边缘存在数。[4]

*  Block Normalization schemes：引入v表示一个还没有被归一化的向量，它包含了给定区间（block）的所有直方图信息。vk 表示 v 的 k 阶范数，这里的 k={1,2}。用 e 表示一个很小的常数。一共4种不同的块规范化schemes

   * L2-morm,
     ![](https://ww1.sinaimg.cn/large/006tKfTcgw1fbfrfgn4coj305l00tq2t.jpg)

   * L2-Hys, 它可以通过先进行L2-norm，对结果进行截短（clipping），然后再重新归一化得到。
   * L1-norm,
     ![](https://ww4.sinaimg.cn/large/006tKfTcgw1fbfrfltceqj304m00ndfp.jpg)

   * L1-sqrt,L1-norm followed by square root
     ![](https://ww3.sinaimg.cn/large/006tKfTcgw1fbfrfl4bwqj305d00r0sm.jpg)

   * 作者发现：采用L2-Hys, L2-norm, 和 L1-sqrt方式所取得的效果是一样的，L1-norm稍微表现出一点点不可靠性。

*  Centre-surround normalization.

### 6.5 Detector Window and Context

### 6.6 Classifier

最后一步就是把提取的HOG特征输入到SVM分类器中，寻找一个最优超平面作为决策函数。作者采用的方法是：使用免费的SVMLight软件包加上HOG分类器来寻找测试图像中的行人。

### 6.7 Discussion

* 在甲酸梯度前进行任何程度的平滑处理都会毁掉HOG的结果，因为许多可供的图像信息都是从细尺度的突出边界形成的。
* 详单，梯度应该在当前金字塔层的最细可供尺度上被计算，修改或者用于方向投票并且只有在那之后在模糊空间。
* 其次，强的局部对比正常化对于良好的结果至关重要，传统的中心环绕样式方案不是最好的选择。
* 更好的结果可以通过相对于不同的局部支持对每个元素（边缘，单元）进行几次标准化，并将结果作为独立信号来实现。

## 7 Summary and Conclusions

* 我们研究了各种描述符参数的影响，并得出结论，在重叠描述符块中的

  * 精细尺度梯度，
  * 精细定向binning，
  * 相对粗糙的空间binning
  * 高质量局部对比度归一化 对于良好的性能都是重要的。
