---
title: Machine Learning Week1
date: 2017-04-25 16:34:47
tags: 
- Machine Learning
category: Machine Learning
keywords:
- Machine Learning
comments: true
---

我对机器学习的理解是：让程序通过学习获得判断的能力，以便程序能在各种情况执行正确的操作。

<!-- more -->

<!-- toc -->

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 机器学习的定义
对机器学习的定义有两种说法：
- 在确定的编程之外，让机器具有学习的能力。
- 一个计算机程序在P的标准下完成任务T，通过经验E进行自我完善，并达到标准T以上，则说明计算机在从经验E中学习。

# 监督性学习（Supervised Learining）

通过已有样本进行训练，从而得到最优模型，在用最优模型将新数据映射为相应结果进行输出，并对输出结果做简单的判断，从而实现分类的目的。

## 回归问题（Regression）

在回归问题中，尝试用连续的输出来预测结果，也就是说，将样本变量映射到连续的函数。比如：在一组人像图片中预测人物的年龄。

## 分类问题（Classification）

在分类问题中，将样本变量映射到特征各异的类别。比如：判断肿瘤病人的肿瘤属于良性还是恶性。

# 非监督性学习（Unsupervised Learning）

事先没有任何样本，直接对数据进行建模，由机器找出数据的特征并进行分类。

## 聚类（Clustering）
找到一种方法自动将百万个基因分组为相似或相关的组，如：生命周期、位置、角色等。

## 非聚类（Non-clustering）
在吵杂的声音中，区分出人的声音和音乐的声音。

# 假设函数（Hypothesis）
使用\\(x^{(i)}\\)来表示输入函数，使用\\(y^{(i)}\\)来表示输出结果，那么就有了一组训练样本(\\(x^{(i)}\\), \\(y^{(i)}\\))，其中\\(i = {1,...,m}\\)。\\(m\\)为训练集的个数。得到训练集后，使用学习算法得到最优\\(h(x)\\)使\\(x{\rightarrow}y\\)，那么就能通过输入预测到正确的输出，如下图，以房价为例：
{% wide_image http://yanqh.com/assets/images/Hypothesis.png "图片来自Coursera"%}
在机器学习中，假设函数为：$$h_{\theta}(X)= {\theta_0}x_1 + ... + {\theta_n}x_n,(X为矩阵，n为特征量，{\theta为参数})$$ 
而参数\\(\theta\\)的值一般为一个向量，需要找到最优的\\(\theta\\)向量，就有了代价函数。

# 代价函数（Cost Function）

{% alert danger no-icon %}
关于代价函数，我没有弄明白它的推导过程，也许以后会明白，先暂时放在这，以后更新。
{% endalert %}

代价函数也叫平方差函数（Squared error function）和均方差（Mean squared error），通过代价函数，选择最优化\\(\vec{ \theta}\\)，使得对于训练集\\((x^{(i)}, y^{(i)})\\)，\\(h_{\theta}(x^{(i)})\\)最接近\\(y^{(i)}\\)，即：$${\mathop{minimize}\limits_{\theta_0,\theta_1} \frac{1}{2m}\sum_{i=0}^m\left(h_\theta(x^{(i)})-y^{(i)}\right)^2}
$$

其中的\\(\frac 1 2 \\)只是为了方便计算，则有了代价函数的一般式（一个特征量）：
$$J(\theta_0, \theta_1) = \frac {1}{2m} \displaystyle \sum _{i=1}^m \left ( {\hat{y}}_i- y_{i} \right)^2 = \frac {1}{2m} \displaystyle \sum _{i=1}^m \left (h_\theta (x_{i}) - y_{i} \right)^2$$

那么接下来的目标就是最优化\\(J(\theta_0, \theta_1)\\)：$$\mathop{minimize}\limits_{\theta_0,\theta_1}J(\theta_0,\theta_1)$$

如何找到最优化\\(J(\theta_0, \theta_1)\\)就有了很多的学习算法，其中就有梯度下降算法。

# 梯度下降（Gradient descent algorithm）

梯度下降算法是一种优化算法，它可以找到代价函数的局部最小值。梯度下降算法不仅可以用在线性回归模型中，在机器学习中的许多其他模型也可以使用。它的思想是，随机抽取\\(\theta_0\\)，\\(\theta_1\\)的值（当特征量只有一个时），然后不断改变\\(\theta_0\\)，\\(\theta_1\\)的值，使\\(J(\theta_0, \theta_1)\\)变小，最终找到\\(J(\theta_0, \theta_1)\\)的最小值点。

在吴恩达教授的课堂上，他用下山的例子来解释梯度下降的过程：
{% wide_image http://yanqh.com/assets/images/GradientDescent1.png "图片来自Coursera"%}
梯度下降算法会收到初始状态的影响，即当从不同的点开始时，可能到达不同的局部最小值，如下图：
{% wide_image http://yanqh.com/assets/images/GradientDescent2.png "图片来自Coursera"%}

下面是梯度算法的演算过程，其中\\(:=\\)表示赋值运算，\\(\alpha\\)表示下降幅度，\\(\frac{\delta}{\delta\theta_j}J(\theta_0, \theta_1)\\)叫做梯度：$$\begin{align}  \text{当 }n=1 \text{ 时，重复直到收敛：} & \lbrace \newline \theta_j := & \theta_j - \alpha \frac \delta {\delta\theta_j} J(\theta_0,\theta_1)，\text{(当j=0和1)}  \newline & \rbrace\end{align}$$

{% alert danger %}
在计算\\(\theta\\)的时候，要同时更新\\(\theta_0\\)和\\(\theta_1\\)。
{% endalert %}
{% alert info %}
由于随着越来越接近最低点，方程中\\(\alpha \frac \delta {\delta\theta_j}\\)的值越来越小，所以不需要减小\\(\alpha\\)的值来下降梯度。
{% endalert %}

梯度为代价函数对\\(\theta\\)的偏导：
$$\begin{align} \text{当 }n=1 \text{ 时，重复直到收敛：} \lbrace & \newline \theta_0 := & \theta_0 - \alpha \frac{1}{m} \sum\limits_{i=1}^{m}(h_\theta(x_{i}) - y_{i}) \newline \theta_1 := & \theta_1 - \alpha \frac{1}{m} \sum\limits_{i=1}^{m}\left((h_\theta(x_{i}) - y_{i}) x_{i}\right) \newline \rbrace& \end{align}
$$

实际上，线性回归的代价函数总是一个凸函数（Convex Function），这样的函数没有局部最优解，如下图：
{% wide_image http://yanqh.com/assets/images/ConvexFunction.png "图片来自Coursera"%}

下图模拟了梯度下降的过程：
{% wide_image http://yanqh.com/assets/images/ConvexFunctionProgress.png "图片来自Coursera"%}

# 本周小结
这一周，学习了机器学习的基本概念，我是先学习了一次，然后再整理了笔记，有一种温故而知新的感觉，特别是对假设函数、代价函数、梯度算法的关系有了更深刻的理解。


