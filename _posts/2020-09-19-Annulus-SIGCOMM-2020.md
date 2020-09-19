---
layout:     post
title:      Annulus SIGCOMM 2020
subtitle:   A Dual Congestion Control Loop for Datacenter and WAN Traffic Aggregates
date:       2020-09-19
author:     Yiran
header-img: img/post-bg-island1.jpg
catalog: true
tags:
    - Congestion Control
    - Transport in Datacenter
---



针对广域网流量和数据中心流量在数据中心内部共享bottleneck带来的性能问题


### Core idea

两个control loop，主要目的是让广域网的流量能够及时获知带宽变化，进行速率调节:

- congestion at nearby datacenter switches (e.g. ToRs) configured to send direct feedback;  借助于QCN 让交换机直接发送反馈
- congestion at other WAN or datacenter switches that do not send direct feedback; 使用已有拥塞控制协议


### Motivation


### Design



<img width="500" height="450" src="/img/post-annulus-1.png"/>




### Implementation and Evaluation



  

### Thoughts




### 参考文献

[Annulus: A Dual Congestion Control Loop for Datacenter and WAN Traffic Aggregates](https://dl.acm.org/doi/10.1145/3387514.3405899)





