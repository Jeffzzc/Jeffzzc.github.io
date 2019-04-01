---
layout:     post
title:      Size-aware Sharding NSDI 2019
subtitle:   Size-aware Sharding For Improving Tail Latencies in In-memory Key-value Stores
date:       2019-4-1
author:     Yiran
header-img: img/post-bg-rwd.jpg
catalog: true
tags:
    - Key-value Store
---

## [Size-aware Sharding For Improving Tail Latencies in In-memory Key-value Stores](https://www.usenix.org/system/files/nsdi19spring_didona_prepub.pdf)

### 解决问题

现有key-value store 99th percentile latency 大, 因为小的 request（请求的item size小）阻塞在大 request（请求的item size大）后面.

论文在background里通过实验说明了request的处理时间与item的size几乎成正比. 这是这篇论文基于size的方法的一个重要observation和前提
### 核心思想 
将小的request和大的request分到不同的core上处理, 避免队头阻塞

### Design
论文的方法比较巧: **分配用于处理小request的核 small core 和专门用于处理大request的核 large core，所有的请求先送往small core, small core查询到item的size，如果超出一个阈值, 就送往large core.**

- request的分配: small core: First, it reads a batch of B requests from its own RX queue. Then it reads a batch of B/ns requests from  the RX queue of the large core (ns 是small core个数)
- small core和large core分配: a cost function that gives us for a request of a given size a certain processing cost. Minos can use various cost functions, but currently uses the number of network packets handled to serve the request as cost, either the number of packets in an incoming PUT request or the number of packets in an outgoing GET reply. The fraction of cores that serve as small cores is set to the ceiling of the fraction of the total processing cost incurred by small requests times the total number of cores
- 适应动态负载: core0专门用于统计size信息 动态确定阈值

### Implementation and evaluation 
   与已有方案（都是**size-unaware**的）对比:

- Multiple queues (nxM/G/1)：硬件dispatch. requests are dispatched immediately (early binding) to a queue for a particular core, often based on a key hash
- Single-queue (M/G/n)：软件dispatch. requests are kept in a single queue and dispatched to a core when it becomes idle (late binding)
- Multiple queues augmented with work stealing：硬件dispatch加上  idle cores steal requests from the queues of other cores（已知最优）

   基于DPDK实现

   实验结果表明能够大大提升99th percentile的latency 同时保持高吞吐。实验很充分


   还评估了以下方面: item-size分布的影响（大request占比、size变化），在read-intensive and write-intensive workloads下的性能, scales with the amount of available network bandwidth, load balancing across cores（尽管request的数目不同的核上不同, 但是packet数目比较均匀）, adapt to changing workload conditions 

   ***实质上是一个tradeoff：拿大request的时间换小request的时间，而大request的占比很小（小于1%）, 小request的完成时间基本都在99th以内***

   **缺点**:除了在高负载下对大request的latency有延迟（相比size-unaware中最好的）, 对CPU的消耗比其他大，会提前saturate CPU, 因为core0需要适应性计算threshold

### My thinking

- 疑惑：实验中由于网卡只支持RSS, 所以To allow the clients and the server to send packets to specific RX queues, we ran a set of preliminary experiments to determine to which port to send a packet so that it is received by a specific RX queue。需要使用支持更加灵活定制的packet redirection NIC（论文举例是说可以用Flow Director）

- ***Size-aware的思想很重要很广泛***：凡是scheduling类的工作都可以从这个角度考虑。cache类：比如 [AdaptSize: Orchestrating the Hot Object Memory Cache in a CDN (NSDI 2017)](https://www.cs.cmu.edu/~harchol/Papers/NSDI17.pdf)首次将object size考虑到cache admission机制中