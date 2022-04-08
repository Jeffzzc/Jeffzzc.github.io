---
layout:     post
title:      SchedulePolicy NSDI 2022
subtitle:   Efficient Scheduling Policies for Microsecond-Scale Tasks
date:       2022-4-07
author:     Yiran
header-img: img/post-bg-mountain4.jpg
catalog: true
tags:
    - OS Scheduling
    - microsecond-scale
---

（未完待续）

在应用微秒级请求的背景下，深入研究负载均衡策略以及核分配策略对latency、CPU efficiency的影响

### Background and Motivation

- 负载均衡策略

- 核分配策略



### Simulation Study





### Evaluation



### Thinking


- 这篇文章通过合理抽象系统中的基本结构和要素，借助仿真实验得到一系列结论，非常经典的系统领域的研究方法

- 几篇文章（相同或者同一个group的作者:Amy Ousterhout;[Adam Belay](http://www.abelay.me/),MIT CSAIL）的研究脉络：[Shenango(NSDI'19)](https://www.usenix.org/conference/nsdi19/presentation/ousterhout) -> [Caladan(OSDI'20)](https://www.usenix.org/conference/osdi20/presentation/fried) -> [Breakwater(OSDI'20)](https://www.usenix.org/conference/osdi20/presentation/cho) -> [SchedulePolicy(NSDI'22)](https://www.usenix.org/system/files/nsdi22-paper-mcclure_2.pdf)

	均关注**microsecond-scale tasks**:

	Shenango: 首次提出使用细粒度的$\mu$s级调度实现CPU高efficiency

	Caladan: 在 Shenango 基础之上，考虑资源竞争，实现更好QoS的 CPU scheduler

	Breakwater: 基于Shenango/Caladan系统，做服务器端 RPC overload control

	SchedulePolicy: 在微秒级请求的背景下，深入研究负载均衡策略以及核分配策略对latency、CPU efficiency的影响




### 参考文献

[Efficient Scheduling Policies for Microsecond-Scale Tasks](https://www.usenix.org/system/files/nsdi22-paper-mcclure_2.pdf)
