---
layout:     post
title:      Rate-Aware Flow Scheduling Infocom 2017
subtitle:   Rate-Aware Flow Scheduling for Commodity Data Center Networks
date:       2019-4-2
author:     Yiran
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Flow Scheduling
---

### 核心思想 

已有流调度工作（[pFabric](https://web.stanford.edu/~skatti/pubs/sigcomm13-pfabric.pdf), [PASE](https://yi-ran.github.io/2019/03/29/PASE-SIGCOMM-2014/), and [PIAS](https://baiwei0427.github.io/papers/pias-ton.pdf)）根据 Shortest Remaining Time First (SRTF)进行调度，认为剩余完成时间仅由流的size决定，没有考虑到应用产生数据的速率。论文设计了RAX，通过获取应用产生数据的速率以及size计算更加准确的剩余完成时间。

### 为什么要考虑data generation rate
<img width="450" height="450" src="/img/post-rax-1.png"/>

<img width="450" height="450" src="/img/post-rax-2.png"/>

- **Data generate rate varies widely across applications and changes dynamically even within an application**: 论文测量了两个真实的应用的数据产生速率.
Data generation rate depends on various factors including application computational complexity, the CPU speed or the I/O throughput of storage devices. It depends only on the server components, such as CPU cores and hard disks, but not on the network. The data generation rate ($r_{g}$) determines the real demand
for the network.

- **Data generation rate impact flow completion times**: A toy example. Flow A and Flow B. The flow sizes are 100MB and 150MB, and the data generation rates are 5 Gbps and 10 Gbps respectively.

<img width="450" height="450" src="/img/post-rax-3.png"/>

### Design
***挑战在于如何在不更改应用的前提下，获取data generation rate.***

- 从$r_{g}$与$r_{t}$的关系考虑，得到结论：**当buffer足够大，$r_{c}$能够准确估计$r_{g}$**

   $r_{g}$ = $r_{t}$时, $r_{g}$ = $r_{t}$ = $r_{c}$; 

   $r_{g}$ > $r_{t}$时, network is bottleneck, data will get buffered. If the buffer is sufficient: data copy from application will not be blocked. $r_{g}$ = $r_{c}$ > $r_{t}$; If the buffer is inufficient: data copy from application will be blocked. $r_{g}$ > $r_{c}$ >= $r_{t}$;

- 计算剩余完成时间
   
   <img width="250" height="250" src="/img/post-rax-4.png"/>

   $r_{g}$根据$r_{c}$来估计: copy的数据量/时间间隔 然后exponentially weighted moving average (EWMA).

   $b_{r}$根据已发送size估计(适用于数据中心重尾分布流量);

   <img width="250" height="250" src="/img/post-rax-5.png"/>

   根据算出的T,优先级调度. Threshold采用指数间隔. 但是会有**乱序**, 论文做法是 temporarily disable the duplicate ACK mechanism at the sender side while the flow priority increases.

### Implementation and evaluation

   主要的提升在**medium flows**. For the web search workload, RAX improves the average FCT over DCTCP and PIAS by 14.9% and 7.8% respectively. RAX outperforms PIAS by medium flows, and breaks even with PIAS by short and long flows. Because PIAS always assigns short flows with the highest priority, it is hard to beat PIAS with short flows. Similarly, long flows (10 MB, ∞) usually stay in the lowest priority queue for both RAX and PIAS. 

   Trace 产生时加入了 specific data generation rate distributions.
 
- Testbed. Linux: leverage jprobes to hook two kernel functions tcp_sendmsg and tcp_sendpage. 测试了rate measurement的准确性、threshold参数设置、buffer大小的影响

- Simulations. 

### My thinking

### 参考文献
[Rate-Aware Flow Scheduling for Commodity Data Center Networks](https://ieeexplore.ieee.org/document/8057082/)

