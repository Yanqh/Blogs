---
title: Machine Learning Week5
date: 2017-05-11 19:16:58
tags:
- Machine Learning
- Neural Networks
category: Machine Learning
keywords:
- Machine Learning
- Neural Networks
---

上一周学习了神经网络的基本概念和公式，这一周将学习神经网络的应用实例。

<!-- more -->

<!-- toc -->

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 添加几个概念

上一周的学习中已经用到了这几个概念，在这里在记录一下：
- L 表示神经网络的层数
- \\(s_l\\) 表示 \\(l^{th}\\) 层的单元个数（不包含偏置单元）
- K 表示输出单元的个数

# 代价函数

在神经网络中，输出层的个数可能不只一个，所以定义 \\({h_\Theta(x)}_k\\) 来表示 \\(k^{th}\\) 的输出，那么代价函数的公式为：
$$\begin{gather} J(\Theta) = - \frac{1}{m} \sum_{i=1}^m \sum_{k=1}^K \left[y^{(i)}_k \log ((h_\Theta (x^{(i)}))_k) + (1 - y^{(i)}_k)\log (1 - (h_\Theta(x^{(i)}))_k)\right] + \frac{\lambda}{2m}\sum_{l=1}^{L-1} \sum_{i=1}^{s_l} \sum_{j=1}^{s_{l+1}} ( \Theta_{j,i}^{(l)})^2\end{gather}$$

{% alert warning no-icon %}
这里正则部分要从 \\(\Theta\\) 的幅度来理解，三个求和公式可以看做是三个循环，先循环每一层，然后循环每一个单元，注意这里的 \\(i\\) 并不是指的训练集的个数，而是由本层的单元个数决定的，即为 \\(s_l\\)（这里是包含偏置单元的，所以在计算的时候要注意去掉），再循环参数的个数，而参数的个数是有下一层的单元个数决定的，即为 \\(s_{l+1}\\)（这里是不包含偏置单元的）。
{% endalert %}

# 反向传播算法（Backpropagation Algorithm）
## 使用步骤
反向传播算法是神经网络用来最优化代价函数的算法。下面主要介绍它的使用过程：

- 对于 \\(m\\) 组训练集 \\(\lbrace (x^{(1)}, y^{(1)}) \cdots (x^{(m)}, y^{(m)})\rbrace\\)
    - 对所有的 \\((l, i, j)\\) 设置 \\(\Delta^{(l)}_{i,j}\\)，那么我们就有了一个全为0的矩阵。
- 设置循环 \\(t = 1 : m\\)
    - 设置 \\(a^{(1)} := x^{(t)}\\)
    - 执行正向传播计算 \\(a^{(l)} \text{  } l = 2, 3, \dots , L\\) ,计算过程见下图1。
    - 使用 \\(y^{(t)}\\) 计算出输出层的误差值（或者叫：敏感度）：
    $$\delta^{(L)} = a^{(L)} - y^{(t)}$$
    - 从右向左计算出每一层的误差值：\\(\delta^{(L-1)}, \delta^{(L-2)},\dots,\delta^{(2)}\\)：$$\delta^{(l)} = ((\Theta^{(l)})^T \delta^{(l+1)})\ .\* a^{(l)}\ .\* (1 - a^{(l)})$$ 这里的 \\(a^{(l)}\ .\*\ (1 - a^{(l)}\\) 可以用S型函数来计算：$$g'(z^{(l)}) = a^{(l)}\ .\*\ (1 - a^{(l)}) = g(z^{(1)})\ .\* (1-g(z^{(1)}))$$
    - 计算每层的 \\(\Delta^{l}\\)：$$\Delta^{(l)}_{i,j} := \Delta^{(l)}_{i,j} + a_j^{(l)} \delta_i^{(l+1)}$$ 也可以使用向量化：$$\Delta^{(l)} := \Delta^{(l)} + \delta^{(l+1)}(a^{(l)})^T$$ 因此，可计算新的 \\(\Delta\\) 矩阵：$$\begin{align} D^{(l)}_{i,j} & := \dfrac{1}{m}\Delta^{(l)}_{i,j} &\text {当 } j = 0 \newline D^{(l)}_{i,j} & := \dfrac{1}{m}\left(\Delta^{(l)}_{i,j} + \lambda\Theta^{(l)}_{i,j}\right)&\text{当 } j \neq 0  \end{align}$$ D矩阵包含了每一层的每个单元 \\(\vec\theta\\)。
{% wide_image http://yanqh.com/assets/images/NeuralNetworkBackpropagationAlgorithm.png "图1,来自Coursera"%}


## 直观理解

{% alert danger no-icon %}
虽然教授在视频中对反向传播做了详细的讲解，但我还是没有明白，只能先记录下来，如下图：
{% endalert %}
{% wide_image http://yanqh.com/assets/images/NeuralNetWorkBackIntuition.png "图1,来自Coursera"%}

# 解包参数（Unrolling Parameters）
在神经网络中，我们操作的是 \\(\Theta\\) 矩阵，或者叫 \\(D\\) 矩阵：$$\begin{align} \Theta^{(1)}, \Theta^{(2)}, \Theta^{(3)}, \dots \newline D^{(1)}, D^{(2)}, D^{(3)}, \dots \end{align}$$ 所以如果传入的是一个长的向量，而使用最优化函数**fminunc()**的时候，那么我们就要先包装，然后解包传出。
## 包装做法

{% tabbed_codeblock %}
      <!-- tab Octave -->
        Theta1 = reshape(thetaVector(1:110),10,11)
        Theta2 = reshape(thetaVector(111:220),10,11)
        Theta3 = reshape(thetaVector(221:231),1,11)
      <!-- endtab -->
{% endtabbed_codeblock %}

## 解包做法
如果 \\(\Theta^{(1)}\\) 的维度为  \\(10 \times 11 \\)，\\(\Theta^{(2)}\\) 的维度为  \\(10 \times 11 \\)，\\(\Theta^{(3)}\\) 的维度为  \\(1 \times 11 \\)，那么解包为：
{% tabbed_codeblock %}
      <!-- tab Octave -->
        Theta1 = reshape(thetaVector(1:110),10,11)
        Theta2 = reshape(thetaVector(111:220),10,11)
        Theta3 = reshape(thetaVector(221:231),1,11)
      <!-- endtab -->
{% endtabbed_codeblock %}

## 使用过程
那么完整的过程如下图：
{% wide_image http://yanqh.com/assets/images/NeuralNetworkUnrollingParameters.png "图1,来自Coursera"%}

# 梯度检查（Gradient Checking）

梯度检查可以检验反向传播是否正确，可以近似的算出代价方程的值：
$$\dfrac{\partial}{\partial\Theta}J(\Theta) \approx \dfrac{J(\Theta + \epsilon) - J(\Theta - \epsilon)}{2\epsilon}$$ 如果展开 \\(\Theta\\) 使用 \\(\Theta_j\\) 来代替的话，我们将得到这样的方程：$$\dfrac{\partial}{\partial\Theta_j}J(\Theta) \approx \dfrac{J(\Theta_1, \dots, \Theta_j + \epsilon, \dots, \Theta_n) - J(\Theta_1, \dots, \Theta_j - \epsilon, \dots, \Theta_n)}{2\epsilon}$$
我们应该选择一个合适的 \\(\epsilon\\)，比如 \\(\epsilon = 10^{-4}\\)，那么我们可以在**Octave**上这么实现：
{% tabbed_codeblock %}
      <!-- tab Octave -->
        epsilon = 1e-4;
        for i = 1:n,
            thetaPlus = theta;
            thetaPlus(i) += epsilon;
            thetaMinus = theta;
            thetaMinus(i) -= epsilon;
            gradApprox(i) = (J(thetaPlus) - J(thetaMinus))/(2*epsilon)
        end;
      <!-- endtab -->
{% endtabbed_codeblock %}

如果反向传播的算法是正确的，那么我们会得到：\\(gradApprox \approx deltaVector\\)。如果验证之后要删除梯度检查的代码，没必要每次都运行，因为这非常耗费性能。

# 随机初始化（Random Initialization）

初始化所有的权重为0会神经网络不起作用，因为得到的下一层的单元将一模一样，这样无论我们设计了多少层其实都只有一个单元，因为它们的值是一样的。所以在初始化的时候，我们可以在一定的范围随机的生成一些数据：
{% wide_image http://yanqh.com/assets/images/NeuralNetworkRandom.png "图1,来自Coursera"%}
这样我们可以得到 \\([-\epsilon,\epsilon]\\) 的值，在**Octave**中我们可以这样做：
{% tabbed_codeblock %}
      <!-- tab Octave -->
        ## If the dimensions of Theta1 is 10x11, Theta2 is 10x11 and Theta3 is 1x11.
        Theta1 = rand(10,11) * (2 * INIT_EPSILON) - INIT_EPSILON;
        Theta2 = rand(10,11) * (2 * INIT_EPSILON) - INIT_EPSILON;
        Theta3 = rand(1,11) * (2 * INIT_EPSILON) - INIT_EPSILON;
      <!-- endtab -->
{% endtabbed_codeblock %}

# 总结一下

当做一个使用神经网络算法的机器学习时，我们先要布局我们的神经网络：有多少层，每个隐藏层有多少神经单元等等，以及已下：
- 输入层的单元个数：特征量 \\(x^{(i)}\\) 的维度
- 输出层的单元个数：分类数量
- 每个隐藏层的单元个数：通常是越多越好，但要符合硬件的计算量
- 默认：一个隐藏单元，如果有多个，推荐每个隐藏层的单元个数保持一致

## 训练过程

1. 使用随机数生成权重（参数）
2. 实现前向传播获得每个 \\(x^(i)\\) 所对应的 \\(h_{\Theta}(x^{(i)})\\)
3. 实现代价函数
4. 实现后向传播计算局部的误差值
5. 使用梯度检查验证后向传播的正确性，然后删除梯度检查的代码
6. 使用梯度下降或者內建的最优化函数最小化代价函数，得到最优参数

# 本周总结

这一周和上一周学习了神经网络的相关知识，虽然写的时候似乎明白一些，不像以前那么懵了，还是要继续巩固理解。


