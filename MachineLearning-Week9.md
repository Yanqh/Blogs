---
title: Machine Learning Week9
date: 2017-06-12 10:48:08
tags: Machine Learning
category: Machine Learning
keywords: Machine Learning
comments: true
---

这一周主要学习了异常检测（Anomaly Detection）和推荐系统（Recommender System），这一周的课听起来不是很难，但推荐系统在做起来的时候，似乎没那么简单。

<!-- more -->
<!-- toc -->
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# 异常检测

异常检测主要用于非监督性学习，但做法很像监督性学习，教授用飞行器的发动机来举例，如下图：
{% wide_image http://yanqh.com/assets/images/MachineLearning9Anomaly1 "图片来自Coursera"%}
这里的异常数据我们如果使用PCA是没有问题的，但我们用图画出来之后，这个数据太过孤立，所以可能是不正常的。那么我们还需要进一步的判断这个数据是否真的有问题。

## 密度检测（Density Estimation）

我们新建立一个模型 \\(p(x)\\)，用来估算这个数据集中是的密度分布，公式为：$$p(x_{test}) < \epsilon \rightarrow Anomaly$$ $$p(x_{test}) \ge \epsilon \rightarrow OK$$
如下图，从数据集中心向外的密度是有大变小的，属于中心周围的数据是正常的，而距离中心的位置越远，越不正常。
{% wide_image http://yanqh.com/assets/images/MachineLearning9Anomaly2.png"图片来自Coursera"%}

## 公式

### 高斯/正态分布（Gaussian/Normal Distribution）

高斯分布也叫正态分布，假设 \\(x \in \mathbb{R}\\)，如果 \\(x\\) 服从期望值为 \\(\mu\\) ，方差为 \\(\sigma^2\\)的高斯分布，则：$$x \sim N(\mu, \sigma^2)$$ $$p(x; \mu, \sigma^2) = \frac{1}{\sqrt{2\pi}\sigma}exp(-\frac{(x - \mu)^2}{2\sigma^2})$$

可以通过下面的图来理解高斯分布：
{% wide_image http://yanqh.com/assets/images/MachineLearning9GaussianDistribution1.png "图片来自Coursera"%}

### 密度检测公式

如果给定数据集 \\(\{x^{(1)},x^{(1)},\dots,x^{(m)}\}\\)，其中 \\(x \in \mathbb{R^n}\\) 对于 \\(x\\) 我们有：$$p(x)=p(x_1;\mu_1, \sigma_1^2) \times p(x_2;\mu_2, \sigma_2^2) \times \dots \times p(x_n;\mu_n, \sigma_n^2) = \prod_{j=1}^np(x_j;\mu_j, \sigma_j^2)$$ 

那么我们要保证：$$x_i \sim N(\mu_n, \sigma_n^2)$$

{% alert info no-icon %}
这里的样本不一定都是服从高斯分布的，但是我们可以使用变化特征量的方式来让数据符合高斯分布的特点。后边也会提到。
{% endalert %}

### 异常检测算法
1. 从数据集 \\(X = \{x^{(1)},x^{(1)},\dots,x^{(m)}\}\\) ，其中 \\(x \in \mathbb{R^n}\\)，假设特征量 \\(x_i\\) 是出现异常的原因。
2. 拟合参数 \\(\mu_1,\dots,\mu_n,\sigma_1^2,\dots,\sigma_n^2\\)：$$\mu_j = \frac{1}{m}\sum_{i=1}^mx_j^{(i)}$$ $$\sigma_j^2 = \frac{1}{m}\sum_{i=1}^{m}(x_j^{(i)}-\mu_j)^2$$
3. 那么拿到新的样本 \\(x\\)，计算 \\(p(x)\\)：$$p(x)=\prod_{j=1}^np(x_j;\mu_j, \sigma_j^2)=\prod_{j=1}^n\frac{1}{\sqrt{2\pi}\sigma_j}exp(-\frac{(x_j - \mu_j)^2}{2\sigma_j^2})$$ 
4. 判断是否为异常数据：$$p(x_{test}) < \epsilon \rightarrow Anomaly$$ $$p(x_{test}) \ge \epsilon \rightarrow OK$$

## 开发和评价一个异常检测系统

### 开发

当开发一个学习算法时，如果我们有一个评价我们学习算法的标准的时候，做出正确的决定是比较简单的。假设我们有一些带有标签的数据，当然这里面包含了正常数据和异常数据（\\(y = 0\\) 为正常数据，\\(y = 1\\) 为异常数据）。

我们用飞行器的引擎为例：

- 10000 个正常的引擎
- 20    个异常引擎

那么我们可以这样来分配我们的样本：

- 训练集：6000个正常引擎
- 交叉验证集：2000个正常引擎，10个异常引擎
- 测试集：2000个正常引擎，10个异常引擎

或者，我们还可以这样分配我们的样本，交叉验证集和测试集是同一个是没有问题的：

- 训练集：6000个正常引擎
- 交叉验证集：4000个正常引擎，20个异常引擎
- 测试集：4000个正常引擎，20个异常引擎

### 评价

1. 在数据集 \\(\{x^{(1)},x^{(1)},\dots,x^{(m)}\}\\) 拟合 \\(p(x)\\) 
2. 在交叉验证集或者测试集长预测：$$ y = \left\[1 \text{  如果 p(x)} < \epsilon(异常)\atop 0 \text{  如果 p(x)} \ge \epsilon(正常)\right.$$
3. 算法评估
    - True positive/false positive/false negative/true negative
    - 查准率、召回率
    - \\(F_1\\) 值
4. 也可以用交叉验证集来选择 \\(epsilon\\)

## 异常检测和监督性学习

使用异常检测的特点：
少量的正样本（20个以内），大量的负样本，异常样本的类型各不相同，难以用学习方法去预测异常样本的样子，异常样本的特征也是我们从未见过的。

异常检测的例子：
欺诈检测、制造业、数据监控中心......

使用监督性学习的特点：

大量的正样本和负样本，足够的正样本来让学习算法区分什么是正样本，什么是负样本。

监督性学习的例子：

垃圾邮件检测、天气预测、癌症分类......

## 特征量的选择

如果你选择的特征量并不符合高斯分布，那么就需要对数据进行处理：
{% wide_image http://yanqh.com/assets/images/MachineLearning9Gaussian.png "图片来自Coursera"%}

# 多维高斯分布（Multivariate Gaussian distribution）

用下图可以看出使用多维高斯分布的必要，图中的蓝色的样本在原始的高斯分布是没有问题的，但是根据图上的信息来看，这两个数据和其他红色的数据不太一样，所以可能存在问题，也就是说我们的原始的高斯分布已经不能完全适用于这个训练集了，这时候我们可以试试多维高斯分布。至于特征量我们可以使用 \\(\frac{x_1}{x_2}\\) 等来添加。
{% wide_image http://yanqh.com/assets/images/MachineLearning9MultivariateGaussian1.png "图片来自Coursera"%}

## 实现过程

1. 参数拟合，通过给定的 \\(\{x^{(1)},x^{(1)},\dots,x^{(m)}\}\\)，计算：$$\mu = \frac{1}{m}\sum_{i=1}^mx^{(i)}$$ $$\Sigma = \frac{1}{m}\sum_{i=1}^m(x^{i}-\mu)(x^{(i)}-\mu)^T$$ 
2. 拿到新的数据，计算： $$p(x) = \frac{1}{(2\pi)^{(\frac{n}{2})}\|\Sigma\|^2}exp(-\frac{1}{2}(x-\mu)^T\Sigma^{(-1)}(x-\mu))$$
3. 如果 \\(p(x) < \epsilon\\) 标记为异常。

## 参数影响
{% wide_image http://yanqh.com/assets/images/MachineLearning9MultivariateGaussian2.png "图片来自Coursera"%}
{% wide_image http://yanqh.com/assets/images/MachineLearning9MultivariateGaussian3.png "图片来自Coursera"%}
{% wide_image http://yanqh.com/assets/images/MachineLearning9MultivariateGaussian4.png "图片来自Coursera"%}
{% wide_image http://yanqh.com/assets/images/MachineLearning9MultivariateGaussian5.png "图片来自Coursera"%}
{% wide_image http://yanqh.com/assets/images/MachineLearning9MultivariateGaussian6.png "图片来自Coursera"%}
{% wide_image http://yanqh.com/assets/images/MachineLearning9MultivariateGaussian7.png "图片来自Coursera"%}

## 与原始模型的比较


| 原始模型 | 多维高斯分布 |
| :-: | :-: |
| 手动结合特征量 | 自动结合特征量 |
| 计算代价低 | 计算代价高 |
| 即使 \\(m\\) 很小也可以运行 | 必须 \\(m > n\\) |

# 推荐系统

推荐系统在我们的日常中有很多，比如给你推荐哪个电影，推荐哪本书，这里先从几个命名开始：

- \\(n_u\\) 表示用户的数量
- \\(n_m\\) 表示电影的数量
- \\(r(i,j) = 1\\) 表示用户 \\(j\\) 评价了电影 \\(i\\)
- \\(y^{(i,j)}\\) 表示用户 \\(j\\) 对电影 \\(i\\) 的评分（假设 \\(r(i,j) = 1\\)）
- \\(\theta^{(j)}\\) 表示用户 \\(j\\) 的参数向量
- \\(x^{(i)}\\) 表示电影 \\(i\\) 的特征向量
- \\(m^{(j)}\\) 表示用户 \\(j\\) 评论过的电影数量

对于用户 \\(j\\) 对电影 \\(i\\) 的评分可以写为 \\((\theta^{(j)})^T(x^{(i)})\\)

## 对于求用户参数

### 优化目标

$$min_{\theta^{1},\dots,\theta^{n_u}} \frac{1}{2}\sum_{j=1}^{n_u}\sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})^2 + \frac{\lambda}{2}\sum_{j=1}^{n_u}\sum_{k=1}^n(\theta_k^{(j)})^2$$

{% alert info no-icon %}
我对 \\(i:r(i,j)=1\\) 的理解为，只计算所有用户 \\(j\\) 评论过的电影。
{% endalert %}

### 梯度下降

$$\theta_k^{(i)} := \theta_k^{(i)} - \alpha\sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} -y^{(i,j)})x_k^{(i)} 当 k = 0 $$

$$\theta_k^{(i)} := \theta_k^{(i)} - \alpha(\sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} -y^{(i,j)})x_k^{(i)} + \lambda\theta_k^{(j)}) 当 k \neq 0 $$

## 对于求电影特征量

## 优化目标

$$min_{x^{1},\dots,x^{n_m}} \frac{1}{2}\sum_{j=1}^{n_m}\sum_{j:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})^2 + \frac{\lambda}{2}\sum_{i=1}^{n_u}\sum_{k=1}^n(x_k^{(i)})^2$$

{% alert danger no-icon %}
这里教授没有说梯度下降的问题，直接进入了协同过滤（Collaborative filtering）
{% endalert %}

## 协同过滤算法

1. 随机初始化较小的 \\(x^{(1)},x^{(1)},\dots,x^{(n_m)},\theta^{(1)},\dots,\theta^{(n_u)}\\) 
2. 实现代价函数：$$J(x^{(1)},x^{(1)},\dots,x^{(n_m)},\theta^{(1)},\dots,\theta^{(n_u)}) = \frac{1}{2}\sum_{(i,j):r(i,j)=1}((\theta^{(j)})^Tx^{(i)} - y^{(i,j)})^2+\frac{\lambda}{2}\sum_{i=1}^{n_m}\sum_{k=1}^{n}(x_k^{(i)})^2 + \frac{\lambda}{2}\sum_{j=1}^{n_u}\sum_{k=1}^n(\theta_k^{(j)})^2$$
3. 最优化代价函数，计算：$$x_k^{(i)} := x_k^{(i)} - \alpha(\sum_{j:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} -y^{(i,j)})\theta_k^{(j)} + \lambda x_k^{(i)})$$ $$\theta_k^{(j)} := \theta_k^{(j)} - \alpha \left(\sum_{i:r(i,j)=1}((\theta^{(j)})^Tx^{(i)} -y^{(i,j)})x_k^{(i)} + \lambda\theta_k^{(j)}\right)$$
4. 利用 \\((\theta^{(j)})^T(x^{(i)})\\) 来预测用户 \\(j\\) 对电影 \\(i\\) 的评分

# 本周总结

在最后我没有加上向量化和实现细节，我觉得已经太多的东西了，我在做题的时候，遇到了问题，在**Github** 上看了一些别人的做法，发现很多很优雅的算法，所以我觉得中周文字的东西虽然很多，但实际的东西还是在做题上，这是最后一个有编程题的课，接下来会去上其他的课。


