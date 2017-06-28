---
title: Machine Learning Week6
date: 2017-05-31 09:44:57
tags:
- Machine Learning
category: Machine Learning
keywords:
- Machine Learning
comments: true
---

<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

这段时间在家里做卫生，一个星期没碰了，先记录一下第六周的学习内容。这一周的内容主要讲验证学习算法的方法、改善机器学习的诊断法和学习模型的设计过程。
<!-- more -->
<!-- toc -->

# 验证的方法

一旦学习算法表现的不尽人意的时候，我们有以下几种方式去改善：
- 获得更多的训练样本
- 尝试较小特征量
- 尝试更多的特征量
- 尝试高阶的特征量
- 增加或减少 \\(\lambda\\)

但这些方法在什么情景下使用呢？这就需要进一步的了解。

## 训练集和测试集

更多的情况是，尽管我们的算法在训练集上表现的很完美，但是在实际应用中却会出现差错（过拟合），所以为了在训练完成之后，能在实际的应用中不出差错，我们需要把样本集分成两部分：训练集（**70%**）和测试集（**30%**），那么我们的步骤就变成了这样：
1. 使用训练集训练 \\(J_{train}(\Theta)\\)，并最小化得到 \\(\Theta\\)
2. 使用得到的模型计算测试集 \\(J_{test}(\Theta)\\) 的误差

计算测试集的误差的方式根据模型的不同，选择不同的方法：
1. 线性回归： $$J_{test}(\Theta) = \frac{1}{2m_{test}} \sum_{i=1}^{m_{test}}(h_\Theta(x^{(i)}_{test}) - y^{(i)}_{test})^2$$
2. 逻辑回归，先求误分类误差： $$err(h_\Theta(x),y) = \begin{matrix} 1 & \mbox{if } h_\Theta(x) \geq 0.5\ and\ y = 0\ or\ h_\Theta(x) < 0.5\ and\ y = 1\newline 0 & \mbox otherwise \end{matrix}$$ 然后我们在根据得到的误分类误差求出平均值，即可得到逻辑回归的测试集误差： $$\text{Test Error} = \frac{1}{m_{test}} \sum^{m_{test}}_{i=1} err(h_\Theta(x^{(i)}_{test}), y^{(i)}_{test}) $$

## 交叉验证

虽然我们可以简单的把训练集分成训练集和测试集，但这样还是会出现误差，比如在选择不同分布的训练集和测试集的时候，误差是不同的，所以我们要加入交叉验证的方法，交叉验证的理解在：[交叉验证](https://zhuanlan.zhihu.com/p/24825503?refer=rdatamining)。
而在我们这里的分类，我们分为训练集（**60%**）、交叉验证集（**20%**），测试集（**20%**），与链接中的描述不同的是，我们这里加入了 \\(d(预测函数的最高次数)\\) 来从训练集中得到不同 \\(d\\) 值的 \\(\Theta^{(d)}\\)，再使用交叉验证集来求出最优的 \\(Theta\\)，最后带入到测试集进行验证算法的效率，如下图：
{% wide_image http://yanqh.com/assets/images/MachineLearningModelSelection.png"图片来自Coursera"%}
虽然讲的时候是这样的，但是在练习的时候，交叉验证是这样做的：

{% tabbed_codeblock %}
      <!-- tab MATLAB -->
    for i = 1:m
        theta = trainLinearReg(X(1:i, :), y(1:i), lambda);
        [error_train(i), grad] = linearRegCostFunction(X(1:i, :), y(1:i), theta, 0);
        [error_val(i), grad] = linearRegCostFunction(Xval, yval, theta, 0);
    end
      <!-- endtab -->
{% endtabbed_codeblock %}

这里和知乎的文章的一致了，我也是看GitHub上一个朋友做的，具体出处忘记了。

# 诊断法

## 高偏差（Bias）和高方差（Variance）

当你的学习方法出现欠拟合的时候，就会出现高偏差，因为误差大。当你的学习方法出现过拟合的时候就会出现高方差，因为你的预测函数太过复杂，或者说出现了多项式高阶的情况。

- 高偏差 \\(J_{train}(\Theta)\\) 和 \\(J_{CV}(\Theta)\\) 都很高，而且 \\(J_{CV}(\Theta) \approx J_{train}(\Theta)\\)。
- 高方差 \\(J_{train}(\Theta)\\) 很低，但 \\(J_{CV}(\Theta)\\) 很高并远大于 \\(J_{train}(\Theta)\\) 。


{% wide_image http://yanqh.com/assets/images/MachineLearningErrorRelationShip.png"图片来自Coursera"%}

## 正则化和高偏差、高方差

当增大 \\(\lambda\\) 的时候，高阶多项式的比重增大，那么误差就越大，会欠拟合。反之会过拟合。所以在选择 \\(\lambda\\) 的时候，我们应该这么做：
1. 创建列表**lambdas**，如：\\(\lambda \in\{0,0.01,0.02,0.04,0.08,0.16,0.32,0.64,1.28,2.56,5.12,10.24\}\\) 
2. 循环**lambdas**，得到相应的 \\(\Theta\\) 值，带入到学习算法，计算交叉验证集的误差（注意这里不要使用正则化，即 \\(\lambda = 0\\)，因为这样才能计算出不同 \\(\lambda\\) 带来的误差大小）。
3. 选择最小误差的 \\(\lambda\\) 和 \\(\Theta\\) 带入到测试集中计算 \\(J_{test}(\Theta)\\)，查看是否符合一般化。

具体的1、2如下：

{% tabbed_codeblock %}
      <!-- tab MATLAB -->
    for i = 1:length(lambda_vec)
        lambda = lambda_vec(i);
        theta = trainLinearReg(X, y, lambda);
        [error_train(i), grad] = linearRegCostFunction(X, y, theta, 0);
        [error_val(i), grad] = linearRegCostFunction(Xval, yval, theta, 0);
    end
      <!-- endtab -->
{% endtabbed_codeblock %}

## 学习曲线（Learning Curves）

绘制学习曲线能帮我们发现问题的原因，一遍我们做出正确的操作。

- 当算法出现高偏差
    - 少量的训练样本：\\(J_{train}(\Theta)\\) 偏低，\\(J_{CV}(\Theta)\\) 偏高。
    - 大量的训练样本：\\(J_{train}(\Theta)\\) 和 \\(J_{CV}(\Theta)\\) 都偏高，而且 \\(J_{CV}(\Theta) \approx J_{train}(\Theta)\\)。
{% wide_image http://yanqh.com/assets/images/MachineLearningHighBias.png"图片来自Coursera"%}
由此可见，出现高偏差的时候，增加训练样本是没有帮助的。
    
- 当算法出现高方差
    - 少量的训练样本：\\(J_{train}(\Theta)\\) 偏低，\\(J_{CV}(\Theta)\\) 偏高。
    - 大量的训练样本：\\(J_{train}(\Theta)\\) 随训练样本的增加而增加， 而\\(J_{CV}(\Theta)\\) 随之降低，而且 \\(J_{CV}(\Theta) > J_{train}(\Theta)\\)。
{% wide_image http://yanqh.com/assets/images/MachineLearningHighVariance.png"图片来自Coursera"%}
由此可见，出现高偏差的时候，增加训练样本是有帮助的。

## 总结

| 修复高偏差（欠拟合） | 修复高方差（过拟合） |
| :-: | :-: |
| 增加特征量 | 增加训练集 |
| 增加多项式次数  | 减少特征量 |
| 降低 \\(\lambda\\)  | 增加 \\(\lambda\\) |

# 学习模型的设计过程

## 垃圾邮件的例子

通过大量的邮件数据，我们可以为每一封邮件构造一个向量，每个元素表示一个单词。这个向量可能包含10,000到50,000个元素，每个元素表示垃圾邮件的常见词汇是否出现，如果出现过就用1表示（垃圾邮件），如果没有出现，就用0表示（正常邮件）。

那么接下来要做什么呢？
- 搜集大量的邮件数据
- 纳入更加复杂的特征量（比如邮件头部的日期）
- 提升算法的对”有意错误“的判断（比如：大小写）

{% alert warning no-icon %}
盲目下手大多数情况下都会面临返工，所以在做之前，应该做好错误分析。
{% endalert %}

盲目下手大多数情况下都会面临返工，所以在做之前，应该做好误差分析。

## 误差分析（Error Analysis）

- 在着手设计模型之前，最好先快速的实现一个简单的算法，然后在交叉验证集在测试
- 绘制学习曲线来决定更多的数据、特征量等等是否奏效
- 手动的测试交叉验证集，尝试定位无法出现的原因

举例来说，比如我们有500封邮件，其中有100封是我们的算法出现了误判。我们应该根据这100封邮件的类型来手动的分析，找到正确判断这些邮件的新的特征量。如果我们发现有些“有意错误”，如fail/failing/failed，那么将这些“有意错误”统一的认为是fail，并加入到我们的模型，如果这能是我们的算法错误率下降，那么这就是指的做的。

## 斜偏数据（Skewed Data）

有些时候，尽管算法在训练集合交叉验证集都有很好的表现，但这并不一定说明算法是完全正确的，比如：某种癌症的患病率是**0.5%**，假设较好的模型M1的正确率是**90%**，但是另外一个模型M2是忽略样本的实际情况，将所有的数据都判定为没有患病，那么M2的准确率就是**99.5%**，那么你能说M2模型是由于M1模型的吗？那肯定不行，这就是偏斜数据。

为了处理偏斜数据，要先明白一些概念，我们把预测的数据和真实的数据进行结合，然后得到了一下的分类：

|  | Acture 1 | Actore 0 |
| :-: | :-: | :-: |
| Predict 1 | True Positive | False Positive |
| Predict 2 | False Negative | True Negative |

### 查准率（Precision）

查准率的概念是，对于所有的数据，我们预测为正样本且实际也为正样本的比例：$$Precision=\frac{True\text{ }Positive}{Predict\text{ }Positive}=\frac{True\text{ }Positive}{True\text{ }Positive + False \text{ }Positive}$$

### 召回率（Recall）

召回率是指对于所有真实的正样本，我们预测为正样本的比例：$$Recall=\frac{True\text{ }Positive}{Acture\text{ }Positive}=\frac{True\text{ }Positive}{True\text{ }Positive + False \text{ }Negative}$$

## 对查准率和召回率的分析

根据对查准率和召回率的定义和计算，我们需要算法体现的二者都很高的情况下，才能说明算法是相对准确的，但是在通常情况下，这两个值不会都很高，当你的算法查准率很高的时候，必定需要召回的很少，那么就有比较低的召回率，反之亦然。可以结合下图来理解：
{% wide_image http://yanqh.com/assets/images/MachineLearningShewedData.png"图片来自Coursera"%}

那么怎么一个才能知道哪一个算法更准确呢？

## \\(F_1\\)值\\(F_1Score\\)

通过直接求查准率和召回率的平均值是不准确的，需要引入\\(F_1Score\\)：$$F_1Score = 2\frac{PR}{P+R}$$
那么如果\\(F_1Score\\)的值越高，算法的表现越好。

{% alert info no-icon %}
在下面的时间，教授引用了：“It's not who has the best algorithm that wins, It's who has the most data.” 
{% endalert %}

# 本周总结
这一周是我在学习之后很久才整理的，用零碎的时间写了两天，还算不错，本周主要学习怎么设计一个学习模型和如果算法出了问题该想那个方向去解决，而不是一头雾水的去尝试每一个想到了的方法。


