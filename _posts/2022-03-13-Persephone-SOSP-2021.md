---
layout:     post
title:      Persephone SOSP 2021
subtitle:   Optimizing Tail-Latency for Heavy-Tailed Datacenter Workloads with Perséphone
date:       2022-3-13
author:     Yiran
header-img: img/post-bg-forest3.jpg
catalog: true
tags:
    - OS Scheduling
    - microsecond-scale
---

（未完待续）

A kernel-bypass OS scheduler designed to minimize **tail latency** for applications executing at **microsecond-scale** and exhibiting **wide service time distributions**

核心思想是 Dynamic Application-aware Reserved Cores (DARC), 感知应用处理时间，为具有短处理时间的请求预留核。



### Background and Motivation


 <img width="950" height="650" src="/img/post-pers-1.png"/>



<img width="450" height="450" src="/img/post-pers-2.png"/>




### Design



<img width="650" height="450" src="/img/post-pers-3.png"/>



### Evaluation



### Thinking

<img width="950" height="650" src="/img/post-pers-4.png"/>



### 参考文献

[When Idling is Ideal: Optimizing Tail-Latency for Heavy-Tailed DatacenterWorkloads with Perséphone](https://irenezhang.net/papers/persephone-sosp21.pdf)
