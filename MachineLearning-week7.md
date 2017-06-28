---
title: Machine Learning week7
date: 2017-06-02 11:40:30
tags:
- Machine Learning
category: Machine Learning
keywords: Machine Learning
comments: true
---

这一周主要学习支持向量机（Support Vector Machine），也叫**SVM**，通常用来进行模式识别、分类和回归分析。

<!-- more -->
<!-- toc -->

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

{% alert danger no-icon %}
对于**SVM**的更详细的使用和优缺点，我也不甚了了，所以先放在这里，等以后明白了在回来修改，这里先记录具体的过程。
{% endalert %}

# 优化目标

以逻辑回归为例，逻辑回归的模型为：$$h_\theta(x)=\frac{1}{1+e^{-\theta^Tx}}$$
我们分析 \\(y=1\\) 和 \\(y=0\\) 的两种情况：
- 当 \\(y=1\\) 时，希望假设 \\(h_\theta(x)\\) 的预测值尽可能的接近1，也就是说希望 \\(z=\theta^Tx \gg 0\\)
- 当 \\(y=0\\) 时，希望假设 \\(h_\theta(x)\\) 的预测值尽可能的接近0，也就是说希望 \\(z=\theta^Tx \ll 0\\)

再从逻辑回归的代价函数来看，逻辑回归的代价函数为：$$J(\theta) = - \frac{1}{m} \sum_{i=1}^m \large[ y^{(i)} \log (h_\theta (x^{(i)})) + (1 - y^{(i)}) \log (1 - h_\theta(x^{(i)}))\large] + \frac{\lambda}{2m}\sum_{j=1}^n \theta_j^2$$
从训练集中任何一个样本来看，对总的代价的影响为：$$-y^{(i)} \log (h_\theta (x^{(i)})) - (1 - y^{(i)}) \log (1 - h_\theta(x^{(i)}))$$
为了使每一个样本对代价函数的影响尽可能的小，分 \\(y=1\\) 和 \\(y=0\\) 的两种情况，当然我们希望代价函数最小为0，但现实情况下是不可能的：
{% wide_image http://yanqh.com/assets/images/MachineLearning7CostFunc.png "图片来自Coursera"%}

而在**SVM**中，这里就出现了改变，我们将曲线的代价函数转变成由两条线段构成的折线：

- 当 \\(y=1\\) 时，希望新的代价函数 \\(cost_1(z)\\) 如左图所示，当 \\(z \geq 1\\) 时，\\(cost_1(z) = 1\\) 
- 当 \\(y=0\\) 时，希望新的代价函数 \\(cost_0(z)\\) 如左图所示，当 \\(z \leq -1\\) 时，\\(cost_0(z) = 0\\) 

{% wide_image http://yanqh.com/assets/images/MachineLearning7CostFunc2.png "图片来自Coursera"%}

那么，用新构建的量个代价函数来替代原来的逻辑回归的代价函数，得到：$$J(\theta) = \frac{1}{m} \sum_{i=1}^m \large[ y^{(i)} cost_1(z) + (1 - y^{(i)}) cost_0(z)\large] + \frac{\lambda}{2m}\sum_{j=1}^n \theta_j^2$$
对上面的代价函数还要做一些调整：
1. 第一项中的 \\(\frac{1}{m}\\) 实际上对最终优化的结果并没有影响，可以去掉
2. 对于**SVM**，我们更希望看到第一项的变化，所以我们通过将多项式整体除以 \\(\lambda\\)，在第一项的参数我们可以用C表示，\\(C=\frac{1}{\lambda}\\)

通过这些调整，我们的代价函数会变成这样：$$J(\theta) = C \sum_{i=1}^m \large[ y^{(i)} cost_1(z) + (1 - y^{(i)}) cost_0(z)\large] + \frac{1}{2m}\sum_{j=1}^n \theta_j^2$$

# 决策边界（SVM Decision Boundary）

SVM有时候也叫做最大间隔分类器（Large Margin Classifier），因为SVM可以找到一个与样本数据集之间有着最大间隔的决策边界。
通过这张图可以看到逻辑回归和SVM的决策边界：
{% wide_image http://yanqh.com/assets/images/MachineLearning7DecisionBoundary.png "图片来自Coursera"%}

这张图中的绿色和红色的是逻辑回归画出来的决策边界，而黑色的是SVM画出来的，我们可以看到，SVM画出的决策边界似乎更合理，蓝色的两条线是决策边界和样本之间的最大间距。

# 正则化参数C

现在我们的优化目标是这样的：$$min_\theta C \sum_{i=1}^m \large[ y^{(i)} cost_1(z) + (1 - y^{(i)}) cost_0(z)\large] + \frac{1}{2m}\sum_{j=1}^n \theta_j^2$$
如果我们选择比较大的C，那么在让代价函数最小化过程中， \\(y=1\\) 或者  \\(y=0\\) 无论我们希望第一项的尽量为0，这样最小化问题就变成了这样：$$min\frac{1}{2}\theta_{j}^2 \text{  }s.t  \left[ {\theta^Tx^{(i)} \ge 1 \text{  }当 \text{  } y^{(i)}=1 \atop \theta^Tx^{(i)} \leq -1\text{  }当 \text{  } y^{(i)}=0} \right.$$

那么当我们选择过大的C，那么可能出现下面的情况：
{% wide_image http://yanqh.com/assets/images/MachineLearning7LargeC.png "图片来自Coursera"%}
如果我们的决策边界像红色的那样，对异常数据都能完美的分辨，那么这并不是一个好消息，因为这可能出现过拟合的情况，所以，我们还是应该选择像黑色这样的决策边界，也就是说C的值不宜过大。

所以回顾 \\(C=\frac{1}{\lambda}\\)：

- C较大时，相当于 \\(\lambda\\) 较小，那么正则化对多项式的次数“惩罚”就比较小，那么可能出现过拟合，高方差。
- C较小时，相当于 \\(\lambda\\) 较大，那么正则化对多项式的次数“惩罚”就比较大，那么可能出现欠拟合，高偏差。

# 核函数（Kernels）

回顾之前我们的高次数多项式的模型来解决无法用直线来分类的问题，为了获得曲线的决策边界，我们的模型可能是像这样的：$$\theta_0+\theta_1x_1+\theta_2x_2+\theta_3x_1x_2+\theta_4x_1^2+\theta_5x_2^2+\ldots$$
但，我们可以用一系列新的特征量f来替换模型中的每一项，例如：$$f_1= x_1,f_2= x_2,f_3= x_1x_2,f_4= x_1^2,f_5= x_2^2,\ldots$$
那么我们的预测函数就可以写成这样：$$\theta_0+\theta_1f_1+\theta_2f_2+\ldots+\theta_nf_n$$

那么用什么方式来做从高次多项式到f的转换呢？那就需要用到核函数了，关于核函数的原理，我看了一下资料，看不懂，后来我想这不用看懂，知道何时用、怎么用就可以了，但是还是记录一下，万一以后看懂了呢？

给定\\(x\\)，计算一个新特征，这个特征依赖于其临近的地标：\\(l^{(1)},l^{(2)},l^{(3)}\\)，那么我们可以这样计算：$$f_1=similarity(x,l^{(1)}) = exp\large(\frac{\|\|x-l^{(1)}\|\|^2}{2\sigma^2})$$  $$f_2=similarity(x,l^{(2)}) = exp\large(\frac{\|\|x-l^{(2)}\|\|^2}{2\sigma^2})$$ $$f_3=similarity(x,l^{(3)}) = exp\large(\frac{\|\|x-l^{(3)}\|\|^2}{2\sigma^2})$$可以看出高斯核的一般式可以这样表示 $$K=kernel(x,l^{(i)})$$
如果给定的 \\(x\\) 和 \\(l^{(1)}\\) 接近：$$f_1\approx exp\large(\frac{0^2}{2\sigma^2})\approx1$$ 反之：$$f_1 \approx exp\large(\frac{0^2}{2\sigma^2})\approx0$$ 

**MATLAB**对核函数的实现也已经写好了，关键你向核函数传入什么。

一般情况下，根据训练集的数量选择地标的数量，如果训练集中有 m 个实例，我们就选取 m 个地标，并：$$l^{1} = x^{1},l^{2} = x^{2},\ldots,l^{m} = x^{m}$$ 这样做的好处是我们得到的新特征 \\(f\\) 是在原特征的基础上得到的，即：$$ f^{(i)}=\left[ \begin{align} & f_0^{(i)}=1 \newline & f_1^{(i)}=sim(x^{(i)},l^{(1)}) \newline & f_2^{(i)}=sim(x^{(i)},l^{(2)}) \newline & \ldots \newline & f_i^{(i)}=sim(x^{(i)},l^{(i)}) = e^0 = 0 \newline & \ldots  \newline & f_m^{(i)}=sim(x^{(i)},l^{(m)}) \end{align}  \right] $$

{% alert info no-icon %}
这里我之前没有理解，现在算是知道怎么回事了，我们是使用一组样本 \\(x^{(i)}\\) 和所有的坐标 \\(l\\) 进行计算，以前理解错了，想的是每次都是一一对应，那么不是每一项都为0吗，现在看了这个公式就一下明白了。
{% endalert %}

# SVM使用高斯核

## 使用

- 选择好参数 \\(C\\) 
- 选择核函数
  - 如果不使用核函数，也叫**线性核函数（Linear Kernel）**，那么就和之前的逻辑回归的做法一样，通过 \\(\theta^Tx \geq 0\\) 来判断 \\(y\\) 的值。
  - 如果使用**高斯核函数（Gaussian Kernel）**，就将核函数带入到**SVM**，修改我们的最优化模型，用 \\(f\\) 代替之前的 \\(X\\)（但这之前要做好特征缩放）：$$min_\theta C \sum_{i=1}^m \large[ y^{(i)} cost_1(\theta^Tf^{(i)}) + (1 - y^{(i)}) cost_0(\theta^Tf^{(i)})\large] + \frac{1}{2m}\sum_{j=1}^m \theta_j^2$$ 为什么正规化的求和公式中的 \\(n\\) 变成了 \\(m\\) 呢？因为这里的特征量个数是 \\(f\\) 而不是之前的 \\(X\\)，而 \\(f\\) 的个数为 \\(m\\) 个。

{% alert danger no-icon %}
在课程中，教授把正则化的那一项修改成了 \\(\theta^TM\theta\\)，说是可以简化计算，而 \\(M\\) 矩阵的值是根据不同的核函数来选取的，这里怎么选取，我还不知道。
{% endalert %}

## 参数\\(\sigma\\)

当 \\(\sigma\\) 过大时，\\(f^{(i)}\\) 过渡的很平缓，这样会出现欠拟合，高偏差，低方差。
当 \\(\sigma\\) 过小时，\\(f^{(i)}\\) 过渡的很尖锐，这样会出现过拟合，低偏差，高方差。

{% alert success no-icon %}
我是这样记忆的，平缓欠拟合，尖锐过拟合。
{% endalert %}

这里用一幅图来加强记忆：
{% wide_image http://yanqh.com/assets/images/MachineLearning7Sigma.png "图片来自Coursera"%}

## 核函数的多个选择

- 多项式核函数（Polynomial Kernel）
- 字符串核函数（String Kernel）
- 卡方核函数（Chi-square Kernel）
- 直方图交集核函数（Histogram Intersection Kernel）
- etc...

教授说，这里的核函数很多，但是连他用的也都很少，所以暂且记下来。

## 多分类问题

一般情况下使用**SVM**来解决二分类的问题，但也有用来解决多分类的问题，好消息是在各个平台的支持多分类问题的**SVM**都已经实现了，可以直接使用。

## 逻辑回归和SVM

在逻辑回归和**SVM**之间我们改怎么选择呢？这里有一些标准：

- 如果相对与 m 来说，n 要大很多，也就是说你有少量的训练样本，但每个样本的特征量很多，那么久应该使用逻辑回归或者是线性核函数的**SVM**
- 如果相对与 m 来说，n 比较小，但 m 也不是很大，比如 n 在1000以内，m 在 10000以内，那么应该使用带高斯核的**SVM**
- 如果相对与 m 来说，n 很小，比如 m 大于 50000，那么应该先寻找或者创造更多的特征量，然后使用逻辑回归或者是线性核函数的**SVM**

{% alert success no-icon %}
但以上的情况，神经网络都可以有较好的表现，只是过程很慢，而使用**SVM**的原因是它的代价函数是凸函数，不存在局部最小值。
{% endalert %}

# 本周总结

这一周的课程我掌握的不好，所以记录的也比较慢，好在写完之后有了一些理解，但作业其实相对之前的还是相对简单，关键还得理解**SVM**。


