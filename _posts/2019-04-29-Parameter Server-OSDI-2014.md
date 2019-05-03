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

### Background 
Machine Learning.

机器学习系统在 web search, spam detection, recommendation systems, computational advertising, and document analysis 有着广泛应用. 这些系统都是从examples中自动学习模型, 也就是 **training data**, 包括三个组成部分：**feature extraction, the objective function, and learning**. 

Feature extraction 处理原始训练数据, 比如 documents, images and user query logs 来获得 ```feature vectors```, 每一个feature就是训练数据的一个属性 （attribute）. 对原始训练数据的 preprocessing 由已有的 MapReduce 等框架完成。


<img width="450" height="450" src="/img/post-PS-1.png"/>


<img width="450" height="650" src="/img/post-PS-2.png"/>



### Challenges

- Communication

- Fault Tolerance



### Design

<img width="450" height="450" src="/img/post-PS-3.png"/>


### Implementation and evaluation





