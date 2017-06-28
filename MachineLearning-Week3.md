---
title: Machine Learning Week3
date: 2017-05-10 15:33:23
tags:
- Machine Learning
category: Machine Learning
keywords:
- Machine Learning
comments: true
---

这一星期的内容主要是分类（Classification）和逻辑回归（Logistic Regression）问题。

<!-- more -->

<!-- toc -->

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 分类（Classification）

分类问题不是简单的线性回归问题，目前，我们着重关注二分类问题，也就是说预测量 \\(y\\) 只包含两个值：0和1。比如说，我们将建立一个邮件的分类器，\\(x^{(i)}\\) 表示一封邮件的特征量，如果 \\(y\\) 的值为1，就表示垃圾邮件，反之表示正常邮件。因此，\\(y\in\{0,1\}\\)。0也叫做负类（Negative Class），1叫做正类（Positive Class）。

# 逻辑方程（Logistic Function）

虽然可以使用线性回归中的假设函数来预测训练集，但是得到的值总会有大于1或则小于0的值，这和分类问题中的 \\(y\in\{0,1\}\\) 并不吻合。因此，需要修改假设函数来使 \\(0 \leq h_\theta(x) \leq 1 \\) 。于是有了逻辑函数，或者叫S型函数（Sigmoid Function）：
$$\begin{align}& h_\theta (x) = g ( \theta^T x ) \newline \newline & z = \theta^T x \newline& g(z) = \dfrac{1}{1 + e^{-z}}\end{align}$$
如果画出来，S型函数是这样的：

{% wide_image http://yanqh.com/assets/images/MachineLearning_SigmoidFunction.png "图片来自Coursera"%}

{% alert warning no-icon %}
这里的 \\(z\\) 其实就是线性回归里的 \\(h_\theta(x)\\)。而\\(g_{(z)}\\) 是可以将实数映射为 \\(\{0,1\}\\) 的函数。
{% endalert %}

如是，\\(h_\theta(x)\\) 给了我们输出值为1的概率，比如，\\(h_\theta(x)=0.7\\)，那么输出值为1的概率为70%，而输出值为0的概率为30%，有以下的公式理解：
$$\begin{align}& h_\theta(x) = P(y=1 | x ; \theta) = 1 - P(y=0 | x ; \theta) \newline& P(y = 0 | x;\theta) + P(y = 1 | x ; \theta) = 1\end{align}$$

# 决策边界（Decision Boundary）

根据前面的介绍，我们可以得到下面的结论：
$$\begin{align}& h_\theta(x) \geq 0.5 \rightarrow y = 1 \newline& h_\theta(x) < 0.5 \rightarrow y = 0 \newline\end{align}$$

而根据S型函数的定义，我们可以得到：
$$\begin{align}& g(z) \geq 0.5 \newline& when \; z \geq 0\end{align}$$

继而得到：
$$\begin{align}& \theta^T x \geq 0 \Rightarrow y = 1 \newline& \theta^T x < 0 \Rightarrow y = 0 \newline\end{align}$$

而决策边界就是分离 \\(y=1\\) 和 \\(y=0\\) 的那条线（可能是线性的，也可能是给线性的），比如下面的例子：
$$\begin{align}& \theta = \begin{bmatrix}5 \newline -1 \newline 0\end{bmatrix} \newline & y = 1 \; if \; 5 + (-1) x_1 + 0 x_2 \geq 0 \newline & 5 - x_1 \geq 0 \newline & - x_1 \geq -5 \newline& x_1 \leq 5 \newline \end{align}$$
在这个例子中，\\(x_1 = 5  \\) 这条直线就是本例的决策分界线。左边全为1，右边全为0。

# 逻辑回归模型

## 代价函数

因为逻辑回归的输出是非线性的，所以不能再使用线性回归的代价函数，也就是说，我们将得到一个凸面的函数：
$$\begin{align}& J(\theta) = \dfrac{1}{m} \sum_{i=1}^m \mathrm{Cost}(h_\theta(x^{(i)}),y^{(i)}) \newline & \mathrm{Cost}(h_\theta(x),y) = -\log(h_\theta(x)) \; & \text{if y = 1} \newline & \mathrm{Cost}(h_\theta(x),y) = -\log(1-h_\theta(x)) \; & \text{if y = 0}\end{align}$$
我们可以通过代价函数和假设函数的图像来看看二者的关系：
{% wide_image http://yanqh.com/assets/images/MachineLearning_LogisticFunctionAndHypothesis1.png "图片来自Coursera"%}
{% wide_image http://yanqh.com/assets/images/MachineLearning_LogisticFunctionAndHypothesis0.png "图片来自Coursera"%}
通过图片，可以得到以下结论：
$$\begin{align}& \mathrm{Cost}(h_\theta(x),y) = 0 \text{ if } h_\theta(x) = y \newline & \mathrm{Cost}(h_\theta(x),y) \rightarrow \infty \text{ if } y = 0 \; \mathrm{and} \; h_\theta(x) \rightarrow 1 \newline & \mathrm{Cost}(h_\theta(x),y) \rightarrow \infty \text{ if } y = 1 \; \mathrm{and} \; h_\theta(x) \rightarrow 0 \newline \end{align}$$

## 简化代价函数

当 \\(y\\) 的取值不同，代价函数的形式也不相同，但是我们可以通过一些数学的技巧来简化代价函数：
$$J(\theta) = - \frac{1}{m} \displaystyle \sum_{i=1}^m [y^{(i)}\log (h_\theta (x^{(i)})) + (1 - y^{(i)})\log (1 - h_\theta(x^{(i)}))]$$

如果使用向量化计算，那么代价函数将更加简洁：
$$\begin{align} & h = g(X\theta)\newline & J(\theta) = \frac{1}{m} \cdot \left(-y^{T}\log(h)-(1-y)^{T}\log(1-h)\right) \end{align}$$

## 梯度下降

和线性回归的梯度下降相同，我们同样可以得到梯度下降的一般式：
$$\begin{align} \text{重复直到收敛：}  & \lbrace \newline \; \theta_j := & \theta_j - \frac{\alpha}{m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)}) x_j^{(i)} \newline  & \rbrace \end{align}$$

向量化的计算：
$$\theta := \theta - \frac{\alpha}{m} X^{T} (g(X \theta ) - \vec{y})$$

{% alert danger %}
这里要同步更新 \\(\theta\\)，而且这里的 \\(h_\theta(x)\\) 是S型函数而不是线性回归中的假设函数。在实际的操作中，不要死套公式，要理解公式中的计算含义。 
{% endalert %}

## 高级最优化函数

除了梯度下降算法，我们还有更多、更好的算法来处理代价函数的最优化，比如**Conjugate**、**BFGS**、**L-BFGS**，而且这些函数在**Ovtave**和**MATLAB**的标准库中都是已经实现和证实过的，所以可以放心使用。

在使用的时候，需要返回一个元组，包含 \\(J(\theta)\\) 和 \\(\dfrac{\partial}{\partial \theta_j}J(\theta)\\)：
{% tabbed_codeblock %}
      <!-- tab Octave -->
        function [jVal, gradient] = costFunction(theta)
            jVal = [...code to compute J(theta)...];
            gradient = [...code to compute derivative of J(theta)...];
        end
      <!-- endtab -->
  {% endtabbed_codeblock %}

而在调用的时候使用**fminunc()**：
{% tabbed_codeblock %}
      <!-- tab Octave -->
        options = optimset('GradObj', 'on', 'MaxIter', 100);
        initialTheta = zeros(2,1);
        [optTheta, functionVal, exitFlag] = fminunc(@costFunction, initialTheta, options);
      <!-- endtab -->
  {% endtabbed_codeblock %}

# 多类分类问题（Multiclass Classification）

当需要分类的问题不再是两个的时候，就需要处理的是多类分类的问题，这时由 \\(y\in\{0,1\}\\) 变为\\(y\in\{1,n\}\\)，这时我们需要分 \\(n+1\\) 个类，但是我们仍然可以用二分类的方法来处理：选择其中一类，然后将其他类化为一个整体，这样就和二分类的问题一样了，如下图：
{% wide_image http://yanqh.com/assets/images/MachineLearning_OneVsAll.png "图片来自Coursera"%}

也就是说，我们要算出每一类的概率（是这一类或不是），然后求出最大的概率就是分类的结果：
$$\begin{align}& y \in \lbrace0, 1 ... n\rbrace \newline& h_\theta^{(0)}(x) = P(y = 0 | x ; \theta) \newline& h_\theta^{(1)}(x) = P(y = 1 | x ; \theta) \newline& \cdots \newline& h_\theta^{(n)}(x) = P(y = n | x ; \theta) \newline& \mathrm{prediction} = \max_i( h_\theta ^{(i)}(x) )\newline\end{align}
$$

{% alert warning no-icon %}
这里我是这样理解的，这里用 \\(h_\theta ^{(i)}(x)\\) 计算出来的不再是一个实数，而是一个向量（两个类别则有两个元素，n个类别则有n个元素），向量中的每一个元素表示了这一类的概率。这里要联系神经网络的课程会更好理解一些。 
{% endalert %}

# 拟合问题

如果假设函数是一条直线（\\(y=\theta_0+\theta_1x\\)）,那么训练集中的很多数据也许都不在直线上，这样的假设函数无疑是不对的。那么，增加特征量的幂次数，让假设函数成为一条凸面的曲线（\\(y=\theta_0+\theta_1x+\theta_2x^2\\)）,就能很好的拟合所有的训练集，但是也不是特征量的幂次数越多，拟合的就很好，先看一张图片：
{% wide_image http://yanqh.com/assets/images/MachineLearning_FittingProblem.png "图片来自Coursera"%}
在图片中，依次为欠拟合（Underfitting）、拟合（Fitting）和过拟合（Overfitting）。可以看到当特征量的幂次数越来越多的时候，假设函数的曲线就越来越复杂，虽然能最好的拟合训练集，但却失去了一般性，在现实意义上，这样的预测是不准确的，也就是说假设函数过于复杂，不能拟合真实的数据。

欠拟合也叫高偏差（High Bias），出现的原因是假设函数过于简单，需要增加特征量来解决。
过拟合也叫高方差（High Variance），出现的原因是假设函数过于复杂，添加了许多无关的特征量，也量种方式解决：
- 减少特征量
    - 手动选择特征量
    - 使用模型选择算法
- 正则化
    - 保持所有的参数，但是减少幂的次数
    - 正则化

# 正则化

## 正则化代价函数
正则化代价函数是在原有的代价函数上增加一项多项式，然后求和并最优化：
$$min_\theta\ \dfrac{1}{2m}\  \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})^2 + \lambda\ \sum_{j=1}^n \theta_j^2$$
其中，\\(\lambda\\) 是正则化参数，决定了正则化的幅度。如果设置过大，可能会导致欠拟合，但如果设置的过小以及为0又可能会导致过拟合，所以如果是过大，可以尝试以 \\(10^{-3}\\) 的倍数进行调整，反之使用 \\(10^{3}\\) 进行调整。

## 正则化和线性回归

### 梯度下降

在线性回归中使用正则化，梯度的计算公式为：
$$\begin{align}  \text{重复直到收敛：}\ & \lbrace \newline \ \ \ \ \theta_0 := &\theta_0 - \alpha\ \frac{1}{m}\ \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})x_0^{(i)} \newline  \ \ \ \ \theta_j := & \theta_j - \alpha\ \left[ \left( \frac{1}{m}\ \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})x_j^{(i)} \right) + \frac{\lambda}{m}\theta_j \right] &\ \ \ \ \ \ \ \ \ \ j \in \lbrace 1,2...n\rbrace\newline & \rbrace \end{align}$$

经过换算也可以写成这样：
$$\theta_j := \theta_j(1 - \alpha\frac{\lambda}{m}) - \alpha\frac{1}{m}\sum_{i=1}^m(h_\theta(x^{(i)}) - y^{(i)})x_j^{(i)}$$

### 正规方程

这里感觉用的地方可能不是很多，暂且记下一个公式：
$$\begin{align}& \theta = \left( X^TX + \lambda \cdot L \right)^{-1} X^Ty \newline& \text{where}\ \ L = \begin{bmatrix} 0 & & & & \newline & 1 & & & \newline & & 1 & & \newline & & & \ddots & \newline & & & & 1 \newline\end{bmatrix}\end{align}$$
\\(L\\) 是第一个元素为0的单位矩阵。

## 正则化和逻辑回归

### 代价函数

加上正则化后的代价函数和之前没有太大的变化：
$$J(\theta) = - \frac{1}{m} \sum_{i=1}^m \large[ y^{(i)}\ \log (h_\theta (x^{(i)})) + (1 - y^{(i)})\ \log (1 - h_\theta(x^{(i)}))\large] + \frac{\lambda}{2m}\sum_{j=1}^n \theta_j^2$$

### 梯度下降

在逻辑回归中使用正则化，梯度的计算公式为：
$$\begin{align}  \text{重复直到收敛：}\ & \lbrace \newline \ \ \ \ \theta_0 := &\theta_0 - \alpha\ \frac{1}{m}\ \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})x_0^{(i)} \newline  \ \ \ \ \theta_j := & \theta_j - \alpha\ \left[ \left( \frac{1}{m}\ \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})x_j^{(i)} \right) + \frac{\lambda}{m}\theta_j \right] &\ \ \ \ \ \ \ \ \ \ j \in \lbrace 1,2...n\rbrace\newline & \rbrace \end{align}$$


{% alert info no-icon %}
逻辑回归和线性回归的梯度下降公式是一样的，但是要注意 \\(h_\theta(x)\\) 的值是不一样的，前面已经说过了。这里的梯度下降都是要剔除 \\(\theta_0\\) 在实际的练习中是将 \\(\vec\theta\\) 的第一个元素置为0，然后计算梯度。
{% endalert %}

# 本周总结

在这一周的总结中发现了很多之前没理解的甚至是理解错了的知识点，看来写Blog是真的有必要！这一周要注意逻辑回归和线性回归的不同，而且逻辑回归在以后的应用可能会更多，所以一定要掌握。


