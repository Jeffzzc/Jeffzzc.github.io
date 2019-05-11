---
layout:     post
title:      Parameter Sever OSDI 2014
subtitle:   Scaling Distributed Machine Learning with PS
date:       2019-4-29
author:     Yiran
header-img: img/post-bg-valley.jpg
catalog: true
tags:
    - Machine Learning
---

## [Scaling Distributed Machine Learning with the Parameter Server](https://www.usenix.org/system/files/conference/osdi14/osdi14-paper-li_mu.pdf)
### 核心思想 

分布式机器学习架构：Parameter Server. A third generation open source implementation of a parameter server that focuses on the systems aspects of distributed inference.
首次将分布式参数服务器架构扩展到支持1000台服务器规模.

### Background 
Machine Learning.

机器学习系统在 web search, spam detection, recommendation systems, computational advertising, and document analysis 有着广泛应用. 这些系统都是从examples中自动学习模型, 也就是 **training data**, 包括三个组成部分：**feature extraction, the objective function, and learning**. 

Feature extraction 处理原始训练数据, 比如 documents, images and user query logs 来获得 ```feature vectors```, 每一个feature就是训练数据的一个属性 （attribute）. 对原始训练数据的 preprocessing 由已有的 MapReduce 等框架完成。

论文给了两类模型：Risk minimization（有监督算法一类，比如预测问题，降低预测错误率）和 Generative Models（无监督算法一类，比如从数据中提取主题，LDA算法）.

Risk minimization：分布式梯度下降算法。样本 $x$ 预测值 $y$ (是否被点击). $x$可用许多属性描述, 每个属性有一个权重$w_{i}$. 学习目标：学习向量$w$. 则可根据$\sum_{i=1}^{d}x_{i}w_{i}$的值预测是否被点击. 损失函数$loss(x,y,w)$代表预测错误率，$\omega$用于惩罚模型的复杂度. 则最终的优化目标函数为：

$$
F(W) = \sum_{i=1}^{n}loss(x_{i},y_{i},w_{i}) + \omega(w)
$$

<img width="450" height="450" src="/img/post-PS-1.png"/>

算法如下：

<img width="450" height="650" src="/img/post-PS-2.png"/>



### Challenges

- Communication：每次模型更新都发送key value 开销大；通信同步带来barrier导致性能下降

- Fault Tolerance：规模大 机器failure多



### Design

<img width="450" height="450" src="/img/post-PS-3.png"/>

- (Key,Value) Vectors.

   例如在前面的Risk minimization问题中，key：属性 value：对应的权重
-  Range Push and Pull
   
   算法1中每个worker将它全部的梯度push给参数服务器，然后pull更新后的weight. Range Push and Pull指支持用户指定范围的push和pull. $w.push(R,dest)$ 指将在范围$R$中的key push给destination，destination可以是一个指定的node或一个node group（如server group）.
-  Flexible Consistency
 
   
-  User-Defined Filters





### Implementation and evaluation

两个算法：Sparse Logistic Regression 和 LDA



