---
title: Machine Learning Week2
date: 2017-05-09 17:48:20
tags:
- Machine Learning
category: Machine Learning
keywords:
- Machine Learning
comments: true
---

这个星期主要介绍梯度下降的多参数情况和正规化方程。

<!-- more -->

<!-- toc -->

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 多元线性回归问题(Multivariate Linear Regression)

当有多个特征量（\\(\geq 2\\)）的线性回归问题称为多元线性回归。其中有以下标记需要记住：
- \\(x_{j}^{(i)}\\)表示第 \\(i^{th}\\)行的第 \\(j\\)个训练样本。
- \\(x^{(i)}\\)表示第 \\(i^{th}\\)行的所有特征的向量。
- \\(m\\)表示训练样本的个数。
- \\(n\\)表示训练样本的个数。

# 向量化(Vectorization)

使用向量化会让计算变的更加简洁，所以假设\\(x_{0}^{(i)} =1 \text{ ，其中 } (i\in { 1,\dots, m } )\\)，那么我们就可以得到 \\(X\\)和\\(\theta\\)：
$$\begin{align}X = \begin{bmatrix}x^{(1)}_0 & x^{(2)}_0 & x^{(3)}_0 \newline x^{(1)}_1 & x^{(2)}_1 & x^{(3)}_1 \end{bmatrix}&,\theta = \begin{bmatrix}\theta_0 \newline \theta_1 \newline\end{bmatrix}\end{align}$$

如是，每个训练集的就会换算成这样：
$$\begin{align}h_\theta(x) =\begin{bmatrix}\theta_0 \hspace{1em} \theta_1 \hspace{1em} ... \hspace{1em} \theta_n\end{bmatrix}\begin{bmatrix}x_0 \newline x_1 \newline \vdots \newline x_n\end{bmatrix}= \theta^T x\end{align}$$

那么对于整个训练集来说，\\(h_\theta(X)\\)的计算就是这样：
$$h_\theta(X) = \theta^TX$$

{% alert warning no-icon %}
这里的 \\(x^{(i)}\\)和 \\(\theta\\)两个向量的元素都为\\(n+1\\)个。在应用的时候不要死套公式，要理解公式的真实意义才能计算正确。
{% endalert %}

# 多元参数下的梯度下降

多元参数的梯度下降和上周的梯度下降只是特征量的个数不同，所以这里的梯度下降算法是这样的：
$$\begin{align} \text{当 }n\geq2 \text{ 时，重复直到收敛：} \; \lbrace & \newline \;  \theta_j := & \theta_j - \alpha \frac{1}{m} \sum\limits_{i=1}^{m} (h_\theta(x^{(i)}) - y^{(i)}) \cdot x_j^{(i)} \; & 当 j := 0...n \newline \rbrace\end{align}$$

{% alert danger %}
这里也是要同步更新 \\(\theta_j\\) 当 \\(j := 0...n\\)，而且这里的 \\(x_{0}^{(i)}\\) 均为 \\(1\\)。
{% endalert %}

# 特征缩放（Feature Scalling）

为了提高梯度下降算法的效率，我们可以适当的缩小特征量到一定的范围，这样可以使 \\(\theta\\) 快速的下降，从而更快的最优化代价函数。比如可以做到：
$$-1\leq x_i \leq 1 \text{ 或 } -0.5\leq x_i \leq 0.5$$
当然，这些值也不是必须的，仅仅是为了让梯度下降的算法更快，可能选到其他的范围，但一定不能太大。但可以通过下面的方法做的规范化：
$$ x_i := \dfrac{x_i - \mu_i}{s_i} \text{ ，其中 }\mu_i \text{ 为第 } i^{th} \text{ 的平均值}，s_i \text{ 为第 } i^{th} \text{ 的范围或标准差}$$
例如，假设 \\(x_i\\)表示房子的价格（\\(100 - 2000\\)），平均值为 \\(1000\\)，那么此时的取值公式为：$$x_i := \dfrac{price-1000}{1900}$$

# 学习幅度（Learning rate）

学习幅度 \\(\alpha \\) 的选择决定了梯度下降的速度，如何正确的选择不是一件简单的事，但可以通过画图的方式来验证 \\(\alpha \\) 是否合适。比如用x坐标表示训练的次数，用y坐标表示对应的 \\(J(\theta)\\) 的值：
{% wide_image http://yanqh.com/assets/images/MachineLearning_LearningRate.png "图片来自Coursera"%}
如果 \\(J(\theta)\\) 的值不是一直减小，那么可能就选错了 \\(\alpha \\) ，需要尝试调整。

# 特征量的多项式回归

这部分的内容我感觉用到的可能不是很多，记录一下即可。

可以通过增加特征量的方程次数来获得额外的特征量，比如，如果假设函数是这样的：\\(h_\theta(x) = \theta_0 + \theta_1 x_1\\)，那么可以通过增加 \\(x_1\\) 次数来达到获得额外特征量的目的。当然我觉得这也要根据现实情况的不同来选择不同的增加方式。假设你的预测值是无限接近某个值，那么要选择的增加方式就是开方而不是平方。

# 正规方程（Normal Equation）

梯度下降是最优化代价函数的一种方式，还有一种方式也可以最优化，比如正规方程：
$$\theta = (X^T X)^{-1}X^T y$$
{% alert danger no-icon %}
关于代价函数，我没有弄明白它的推导过程，也许以后会明白，先暂时放在这，以后更新。
{% endalert %}

那么可以对比一下两者的使用区别：

| 算法 | 梯度下降 | 正规方程 |
| :-: | :-: | :-: |
| \\(\alpha\\) | 需要 | 不需要 |
| 重复步骤 | 需要 | 不需要 |
| 复杂度 | \\(O(kn^2)\\) | \\(O(n^3)\\) |
| 当 \\(n\\) 比较大时 | 运行良好 | 非常缓慢 |

{% alert warning no-icon %}
正规方程在用的时候需要考虑\\(X^{T}X\\)是否可以求逆，而且这个算法只适应特征量不是很多的时候，所以现实情况下用的不多，因为现实中特征量的个数一定不会少。
{% endalert %}

# 本周总结

这一周的内容主要学习了多元梯度下降的公式，以及Octave和Matlab的安装和使用，多使用和理解就好了。


