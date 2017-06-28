---
title: Machine Learning Week4
date: 2017-05-11 16:28:06
tags:
- Machine Learning
- Neural Networks
category: Machine Learning
keywords:
- Machine Learning
- Neural Networks
comments: true
---

这一周学习神经网络（Neural Networks）的相关内容。

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

<!-- more -->

<!-- toc -->

# 神经网络的简介

人体的神经系统是有树突（Dendrites）和轴突（Axons）组成的，树突负责接收外部的信息，而轴突负责输出结果。基于这些，我们设计神经网络模型时，输入的特征量 \\(x_1 \cdots x_n\\) 就像是树突，而输出量 \\(h_\theta(x)\\) 就好像是轴突。需要注意的是，在计算的时，我们会添加一个外的值为1的 \\(x_0\\)，叫做偏置单元（Bias Unit）。在神经网络中，我们依然使用和分类中相同的S型函数，但在这里叫做激励函数（Activation Function），并且在这里的 \\(\theta\\) 叫做权重（Weight）。

如果用向量去看，就像这样：
$$\begin{bmatrix}x_0 \newline x_1 \newline x_2 \newline \end{bmatrix}\rightarrow\begin{bmatrix}\ \ \ \newline \end{bmatrix}\rightarrow h_\theta(x)$$

# 神经网络的相关公式

我们的输入节点（Layer 1）也叫输入层（Input Layer），从输入节点将数据传送下一节点（Layer 2），最终导出假设函数，也叫输出层（Output Layer）。中间节点可能有多个，所以中间节点也统称为隐藏层（Hidden Layers）。

举例来说，我们标记这些隐藏层为 \\(a^2_0 \cdots a^2_n\\) 也叫做激励单元（Activation Unit）。
$$\begin{align}& a_i^{(j)} \text{ 表示 } j \text{ } 层中第\text{ }i^{th}\text{ }个激励单元  \newline& \Theta^{(j)} \text{ 表示 } j \text{ } 层到\text{ }(j+1)\text{ } 层的权重 \end{align}$$

## 理解公式

如果只有一个隐藏层，那么模型就想是这样的：
$$\begin{bmatrix}x_0 \newline x_1 \newline x_2 \newline x_3\end{bmatrix}\rightarrow\begin{bmatrix}a_1^{(2)} \newline a_2^{(2)} \newline a_3^{(2)} \newline \end{bmatrix}\rightarrow h_\theta(x)$$
其中每一个激励节点是这样得到的：
$$\begin{align} & a_1^{(2)} = g(\Theta_{10}^{(1)}x_0 + \Theta_{11}^{(1)}x_1 + \Theta_{12}^{(1)}x_2 + \Theta_{13}^{(1)}x_3) \newline & a_2^{(2)} = g(\Theta_{20}^{(1)}x_0 + \Theta_{21}^{(1)}x_1 + \Theta_{22}^{(1)}x_2 + \Theta_{23}^{(1)}x_3) \newline & a_3^{(2)} = g(\Theta_{30}^{(1)}x_0 + \Theta_{31}^{(1)}x_1 + \Theta_{32}^{(1)}x_2 + \Theta_{33}^{(1)}x_3) \newline & h_\Theta(x) = a_1^{(3)} = g(\Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{(2)}a_1^{(2)} + \Theta_{12}^{(2)}a_2^{(2)} + \Theta_{13}^{(2)}a_3^{(2)}) \newline \end{align}$$
这里的 \\(\Theta^{(j)}\\) 不在是之前的向量，而是矩阵，\\(\Theta^{(j)}\\) 的维度是根据本层和下一层的单元个数来的，比如 \\(j\\) 层中有 \\(s_j\\) 个单元，\\(j+1\\) 层中有 \\(s_{j+1}\\) 个单元，那么 \\(\Theta^{(j)}\\) 的维度就为 \\(s_{j+1} \times (s_j + 1)\\) 

## 进化公式

定义一个变量 \\(g_k^{j}\\) 来代替上面 \\(g\\) 方程中的参数，那么公式可以写成这样：
$$\begin{align}a_1^{(2)} = g(z_1^{(2)}) \newline a_2^{(2)} = g(z_2^{(2)}) \newline a_3^{(2)} = g(z_3^{(2)}) \newline \end{align}$$

若 \\(j=2\\)，对于 \\(k^{th}\\) 单元有： 
$$z_k^{(2)} = \Theta_{k,0}^{(1)}x_0 + \Theta_{k,1}^{(1)}x_1 + \cdots + \Theta_{k,n}^{(1)}x_n$$

用向量表示 \\(x\\) 和  \\(z^{j}\\) ：
$$\begin{align}x = \begin{bmatrix}x_0 \newline x_1 \newline\cdots \newline x_n\end{bmatrix} &  \text{  } z^{(j)} = \begin{bmatrix} z_1^{(j)} \newline z_2^{(j)} \newline\cdots \newline z_n^{(j)}\end{bmatrix}\end{align}$$

假设 \\(x = a^{(1)}\\) 那么可以得到：
$$z^{(j)} = \Theta^{(j-1)}a^{(j-1)}$$
公式中的矩阵 \\(\Theta^{(j-1)}\\) 的幅度为 \\(s_j \times (s_{j - 1} + 1)\\)，而向量 \\(a^{(j-1)}\\) 的幅度为 \\((s_{j - 1} + 1) \times 1\\)，所以我们得到向量 \\(z^{(j)}\\) 的幅度为 \\(s_j \times 1\\)，这相当于得到了逻辑回归中的 \\(z\\)，在用S型函数，就能得到下一层的各个单元的值，所以公式可以写为：
$$a^{(j)} = g(z^{(j)})$$
那么总结我们的终极公式：
$$z^{(j+1)} = \Theta^{(j)}a^{(j)}$$
{% alert info no-icon %}
这里的理解需要结合之前学习的逻辑回归，每两层就想进行了一次逻辑回归，得到的值不是作为预测值，而是作为下一层的特征量，再进行下一层的单元值，当然，计算的时候要记得加上偏置单元。
{% endalert %}

# 本周总结
这一周的内容主要是学习神经网络的基础概念和公式，下一周学习具体的使用。


