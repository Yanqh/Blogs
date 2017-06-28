---
title: Machine Learning Week8
date: 2017-06-05 15:17:36
tags: Machine Learning
category: Machine Learning
keywords: Machine Learning
comments: true
---

这一周主要学习了非监督性学习的算法K均值（K-Mean）、维度约减（Dimensionality Reduction）和主成分分析（Principal Components Analysis, PCA)。

<!-- more -->
<!-- toc -->
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# K均值

K均值算法的目标是将有紧密关系的子集（簇）聚集起来，这种方法是一种迭代方法，算法输入的是簇的个数K，训练集是无标签的数据集。

## 算法的理解

K均值算法分为两步，第一步是进行簇分类，我们要遍历所有的样本，根据每个样本到聚类中心点的距离，将样本聚类到没最短距离的簇。第二步是移动聚类中心，如果聚类中心的并不是这一类的中心点，所以在聚类之后还有进行移动，以致中心点的位置处于中心。

{% wide_image http://yanqh.com/assets/images/MachineLearning8K_Mean.png "K-Mean"%}

## 算法的实现

1. 随机初始化K个簇中心点\\(\mu_1,\mu_1,\dots,\mu_K \in \mathbb{R}^n\\)
2. 算法核心：$$\begin{align}  \text{重复直到收敛：}\ & \lbrace \newline & c^{(i)} := j，min \|\|x^{(i)} - \mu_j\|\|^2 \text{，其中 }i \in [1, m] \newline & \mu_k := \frac{1}{\|C_k\|}\sum_{i \in C_k}^mx^{(i)} \text{，其中 }k \in [1,K]\newline & \rbrace \end{align}$$

符号解释：

- \\(c^{(i)}\\) 表示样本 \\(x^{(i)}\\) 所归属的簇索引
- \\(\mu_k\\) 表示第 \\(k\\) 个簇聚类中心
- \\(\mu_{c^(i)}\\) 表示样本 \\(x^{(i)}\\) 所归属的簇

优化目标：$$minJ(c^{(1)},\dots,c^{(m)},\mu_1,\dots,\mu_k)=\frac{1}{m}\sum_{i=1}^{m} \|\|x^{(i)} - \mu_{c^{(i)}}\|\|^2$$

## 随机初始化K

选择K的值没有正确的方法，有的是随机K，然后多次聚类求最小的 J。也有的使用肘部法则（Elbow Method），但这里也不一定有作用，所以教授在最后说可以使用细化的目标来分类，比如你想分类T-shirt的尺寸，你可以分类K=3（S,M,L）或者K=5(XS,S,M,L,XL)。

# 维度约减

维度约减是为了压缩数据，节省内存和磁盘，使特征量的维度降低来加速算法的效率，也适用于可视化数据（1D/2D/3D）。主成分分析是常用的降维方法。
从2D到1D:
{% wide_image http://yanqh.com/assets/images/MachineLearning82D.png "图片来自Coursera"%}
从3D到2D:
{% wide_image http://yanqh.com/assets/images/MachineLearning83D.png "图片来自Coursera"%}

## 主成分分析(PCA)

### 目的

PCA的目的是达到降维的目的，以2D降维到1D为例，是为了将数据点投影到直线上，而且要是投影的距离最短，所以如下图，PCA会选择红色的直线而不是品红的直线：
{% wide_image http://yanqh.com/assets/images/MachineLearning8PCA.png "图片来自Coursera"%}

### 实现过程

1. 数据预处理
    - 求均值 $$\mu_j = \frac{1}{m}\sum_{i=1}^mx_j^{(i)}$$     
    - 特征量替换 $$x_j^{(i)}=x_j^{(i)}-\mu_j$$
    - 如果特征量之间的差距比较大，还需要做特征缩放，其中\\(s_j\\) 为平方差 $$x_j^{(i)}=\frac{x_j^{(i)}-\mu_j}{s_j}$$
2. 求解协方差矩阵 $$\Sigma=\frac{1}{m}\sum_{i=1}^n(x^{(i)})(x^{(i)})^T$$
3. SVD求解特征值和特征向量 $$[U,S,V]=svd(\Sigma)$$
4. 此时，\\(\mu^{(i)} \in \mathbb{R}^n\\)我们得到的 $$U = \left[\mu^{(1)},\mu^{(2)},\dots,\mu^{(n)},\right] \in \mathbb{R}^{n \times n}$$
5. 而我们需要的值是k维，所以还需要截取 $$U_{reduce} = U(:, 1:k)$$
6. 那么新的k维的特征量数据集为：$$z=U_{reduce}^Tx，其中x\in\mathbb{R}^n，不包含x_0=1$$
7. 使用新的特征量带入到学习算法。

### K维度如何选择

我们注意到在SVD的返回值中还有一个值S矩阵：
{% wide_image http://yanqh.com/assets/images/MachineLearning8SVD.png "图片来自Coursera"%}

那么我们可以这样算 k：$$\frac{\sum_{i=1}^kS_{ii}}{\sum_{i=1}^mS_{ii}} \geq 0.99，循环取得最小的k$$

这里的0.99叫做保留了99%的差异，这里的可以修改的如：95%、90%等。

### PCA的其他应用

PCA也能应用在监督性学习，是用来降维，但是不能用来解决过拟合的问题（我也没有做过，只是记录）。

# 本周总结

这一周学习了两个算法，感觉用的最多的是和非监督性学习有关，做完了练习题，其实感觉现在的东西没有之前那么难了，也可能是没有理解透，以后遇到了相关的问题再回来看吧。


