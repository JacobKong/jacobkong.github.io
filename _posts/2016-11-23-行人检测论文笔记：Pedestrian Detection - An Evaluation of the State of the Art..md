---
layout: post
title: 行人检测论文笔记：Pedestrian Detection - An Evaluation of the State of the Art
date: 2016-11-23 15:32:24.000000000 +09:00
---

## 知识点

- 对数正态分布（lognormally distributed）：对数为正态分布的任意随机变量的概率分布。
  - 如果 X 是正态分布的随机变量，则 exp(X)为对数正态分布.
  - 如果 Y 是对数正态分布，则 ln(Y) 为正态分布。
  - 如果一个变量可以看作是许多很小独立因子的乘积，则这个变量可以看作是对数正态分布。
  - 对数正态分布的概率密度函数为：

![](https://ww2.sinaimg.cn/large/006tKfTcjw1fbfr6mn9ccj308q01ldfr.jpg)

- 对数平均：对数平均与几何平均相等，并且比算数平均，对于对数正态分布数据的典型值更具代表性
  - 二个数字的对数平均小于其算术平均，大于几何平均，若二个数字相等，对数平均会等于算数平均及几何平均。

![](https://ww4.sinaimg.cn/large/006tKfTcjw1fbfr6kv30dj30f701mmx5.jpg)

- Histogram of Oriented Gradients for Objection Detection.(HOG)步骤：
  - Sampling positive images
  - Sampling negative images
  - Training a Linear SVM
  - Performing hard-negative mining
  - Re-training your Linear SVM using the hard-negative samples
  - Evaluating your classifier on your test dataset, utilizing non-maximum suppression to ignore redundant, overlapping bounding boxes
- NMS:Non-maximum Suppression(非极大值抑制):可看成一种局部极大值搜索，这里的局部极大值要比他的邻域值都要大。这里的邻域表示有两个参数：维度和n-邻域。
- LBP: Local Binary Patterns

## Abstract

- 单目图像的行人检测方法持续的在发展。
- 多种数据集+广泛变化的评估方法->导致方法之间直接的比较很困难。
- 三个贡献：
  - 数据集；
  - 精炼的pre-image评估方法；
  - 对现有state-of-art检测器进行比较评估。
- 行人检测在 **低像素** 和 **部分遮挡** 行人情况下依旧表现失望。

## 1 Introduction

- 对现有行人检测方法的一些疑问：
  - Do current detectors work well?
  - What is the best ap- proach?” “What are the main failure modes?
  - What are the most productive research directions?

### 1.1 Contributions

- Data set.
  - 350,000行人标注框BB
  - 250,000帧
  - 遮挡和时间上相似的也被标注。
  - 对行人等级、遮挡、位置进行了统计
- Evaluation methodology.
- Evaluation.
  - 评估了16个有代表性的先进的行人检测器
- 两个团队发布的surveys与作者的工作是互补的。
  - Geronimo——先进的司机助手系统中的行人检测。
  - Enzweiler and Gavrila——发布Daimler检测数据集

## 2 The Caltech Pedestrian Data Set

### 2.1 Data Collection and Ground Truthing

- 当一个标注器在至少两帧中在同一个行人上标注了边界框，则边界框利用3次插值在中间帧进行标注
- BB-full
- BB-vis: 被遮挡行人可见区域标注框
- 三种标注：
  - Person
  - People
  - Person?

### 2.2 Data Set Statistics

- 行人的高度、宽度都类似对数正态分布。
- 如果多变量中的每个变量符合对数正态分布，则这些变量的线性组合也符合正态分布。
- BB 横纵比 w/ h, log(w /h) = log(w)-log(h).
- 由于行人姿势（手、肘）的原因，会导致行人宽度变化。
- h=Hf/ d. H=1.8m, d=1800 /hm
- 大部分行人都被观察为medium scale，为了安全系统，检测也必须发生在这个scale。
- 通过对遮挡情况的统计，总体来说，遮挡情况远远没有统一， **利用这个发现可以挡住提高行人检测器的性能。**
- 不仅遮挡是高度不一致的， **遮挡的类型也是有明显额外的结构。**
- 通过对比ground truth和HOG检测器检测出的行人位置进行同样的统计对比，有如下的图，可以发现，利用行人位置这一约束条件可以合理的加快检测但是只能适当的减少假正例。

![](https://ww2.sinaimg.cn/large/006tKfTcjw1fbfr6mn9ccj308q01ldfr.jpg)

### 2.3 Training and Testing Data

- 四种训练/测试情景
  - Scenario ext0:
  - Scenario ext1:
  - Scenario cal0:
  - Scenario cal1:
- 作者应该在检测器开发过程中使用 ext0/cal0，并且只在完成所有参数后再 ext1/cal1下进行评估。

### 2.4 Comparison of Pedestrian Data Sets

- Imaging setup.
- Data set size.
- Data set type.
- Pedestrian scale.
- Data set properties.

## 3 Evaluation Methodology

### 3.1 Full Image Evaluation

- 对阈值小于0.6的评估就不用关心了。
- 具有最高置信度的检测首先被匹配；如果一个被检测到的BB匹配到多个ground truth边界框，则具有最高覆盖率的匹配将被使用。
- 没有被匹配到的BBdt算作假正例，没有被匹配道德BBgt被称为假负例。
- 通过改变检测置信度的阈值，画出miss rate-FPPI的曲线图来比较各个检测器。这种图比准确率-召回率的图更好，因为对于汽车应用，as typically there is an upper limit on the acceptable false positives per image rate independent of pedestrian density.
- 利用 **对数平均 遗漏率** 来总结检测器的性能。在（10^-2~10^0）范围的对数空间中，通过9个FPPI率平均计算miss rate来得到

### 3.2 Filtering Ground Truth

- BBig: 被选择忽略的Ground truth. 被忽略的区域。
- 将BBgt设为被忽略和丢弃掉这个样本是不一样的；后者代表这个样本是一个假正例。

### 3.3 Filtering Detections

- 考虑三种可能的过滤策略：
  - strict filtering: 在匹配之前删除所选范围之外的所有检测。
  - postfiltering: 在所选评价范围外的检测允许与范围内的BBgt匹配。
  - expanded filtering: 类似于严格过滤，除了在评估之前去除扩展评估范围之外的所有检测
- Expanded filtering 在 strict filtering 和 postfiltering之间做了很好的妥协。
- 在整个评估工作中，我们使用expanded filtering(r=1.25)。

### 3.4 Standardizing Aspect Ratios

- 标准化GT和DT的aspect ratio，这样做会从检测器设计中删除无关的任意选择，并有助于性能比较。
- 一般来说，探测器的长宽比取决于开发过程中使用的数据集，通常在训练后选择。
- 我们建议将所有BB标准化为0.41的长宽比（Caltech数据集中的对数 - 平均长宽比）。
- 我们保持BB高度和中心固定，同时调整宽度.
- 重要的是检测器和ground truth纵横比匹配。

### 3.5 Per-Window versus Full Image Evaluation

- PM 评估方法通常用来比较分类器（检测器的返利）或者用来评估系统对于自动兴趣区域生成的性能。
- PW结果是从其 **原始出版物** 中产生的。
- 全图像结果是通过评估同一行人但在其 **原始图像上下文** 中获得的。
- 将一个二分类转化为一个检测器所做的选择包括：
  - 包括空间和尺度跨度
  - 非最大抑制的选择。会影响图像的性能。
  - 在PW评估期间测试的窗口通常不同于在全图像检测期间测试的窗口，
- 假阳性可能来自对身体部位或不正确的尺度或位置的检测
- 假阴性可能源于被测试的窗户和真实的行人位置或来自NMS之间的轻微不对准。

![](https://ww4.sinaimg.cn/large/006tKfTcjw1fbfr6loh02j30as08vwey.jpg)

## 4 检测算法

### 4.1 Survey of the State of the Art

- Papageorgiou and Poggio [16]提出了第一个滑动窗口检测器。将支持向量机（SVM）应用于多尺度Haar小波的过度完备字典。
- Viola和Jones( **VJ** )[44]基于这些想法，引入了用于快速特征计算的积分图像和用于有效检测的级联结构，以及利用AdaBoost进行自动特征选择。这些想法继续作为现代探测器的基础。
- 随着基于梯度的特征的采用带来了巨大的收益。
- 受SIFT [45]启发，Dalal和Triggs [HOG][7]通过显示相对于基于强度的特征的实质性增益，普及了用于检测的定向梯度特征的直方图（HOG）。
- 现在，HOG特征的变体的数量已经大大增加，几乎所有现代检测器以某种形式利用它们。
- Shape features（形状特征）也是一个用于检测经常用到的线索.
- Boosting用于学习头部，躯干，腿部和全身检测器.
- Shapelets: 是从局部区块中的梯度辨别地学习的形状描述符.
- Boosting用来将多个Shapelet结合成一个整体的检测器。
- Motion是人类感知的另一个重要提示; 然而，成功地将运动特征结合到检测器中已证明对于移动的相机具有挑战性。
- 虽然没有迹象表明单个特征由于HOG，但附加特征可以提供一些互补信息。

### 4.2 Evaluated Detectors

- 直接从作者处得到提前训练好的检测器。
- 这些检测器通常遵循滑动窗口范例，其需要对检测窗口进行特征提取，二分类和密集多尺度扫描，随后进行非极大值抑制。
- Features：几乎所有的现代检测器都使用了都写形式的梯度直方图。
- Learning：因为它们的理论保证，可扩展性和良好的性能，支持向量机[16]和boosting[44]是最受欢迎的选择。
- Boosting自动执行特征选择。一些检测器（在“特征学习”列中用标记指示）在分类器训练之前或与分类器训练一起学习更小或中等大小的特征集合。
- Detection details：两种主要的非最大抑制方法：
  - Mean shift(MS)模型估计
  - Pairwise max(PM)抑制：根据充分的重叠丢弃可信度较低的每对检测
  - PM*：允许检测去匹配另一检测的任意子区域。
- Implementation notes

## 5 Performance Evaluation

### 5.1 Performance on the Caltech Data Set

- Overall：绝对性能很弱。
- Scale
- Occlusion
- Reasonale：性能在中等规模或部分封闭的行人的检测很差，而对于远距离或在重度封闭的情况下，它的性能特别差。这促使我们评估超过50像素高的行人在没有或部分遮挡（这些在没有很多上下文的情况下清晰可见）的性能。 **我们将这称为合理的评估设置。**
- Localization

### 5.2 Evaluation on Multiple Data Sets

### 5.3 Statistical Significance（统计显著性）

- 关键的洞察力是将每个数据集上的绝对性能转换为算法排名，从而消除不同数据集难度的影响。
- 我们使用非对数Friedman检验和posthoc分析来分析统计学显着性.
- 对于我们的分析，我们使用 **非参数Friedman测试** 以及 **Shaffer posthoc test**
- 我们基于其对数平均丢失率（在合理的评估设置下测试）对每个数据折叠上的检测器进行排名。 该程序为14个检测器得到做总共28个排名。

### 5.4 Runtime Analysis

- 总的来说，运行时和精度之间似乎没有很强的相关性。

## 6 Discussion

- 应该注意，单帧性能是整个系统性能的下限，跟踪，上下文信息和额外传感器的使用可以帮助减少假警报并提高检测率（参见[2]）。