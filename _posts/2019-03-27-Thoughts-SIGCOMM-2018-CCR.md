---
layout:     post
title:      Thoughts of Programmable Switches SIGCOMM 2018 CCR
subtitle:   The Role of Programmable Switches
date:       2019-3-27
author:     Yiran
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - Programmable Switches
---


### 核心思想

- 在可编程交换机上实现的有两类：**应用功能（Load Balancer比如[SilkRoad](https://eastzone.bitbucket.io/paper/sigcomm17-silkroad.pdf)、Load partition比如[NetCache](https://www.cs.jhu.edu/~xinjin/files/SOSP17_NetCache.pdf)） 和 网络功能 (Network traffic balancing、Network Telemetry、Packet scheduling、Congestion Control)**
- 其实几乎所有的应用的offload都可以通过server+switch实现，server实现计算处理和大量的状态存储，switch只做forward功能来达到相似的性能；将应用卸载到可编程交换机上带来了很多实现的问题 反而会影响应用本身的逻辑
- 网络功能在可编程交换机上的应用，没有什么替代的方式，因为这类功能都需要获取及时细粒度的网络状态 只能在交换机上实现


### 参考文献
[Thoughts on Load distribution and the Role of Programmable Switches](https://dl.acm.org/citation.cfm?id=3314216)
