---
layout:     post
title:      Paper collection of HotNets 2022
subtitle:   Paper collection of HotNets 2022
date:       2022-12-02
author:     Yiran
header-img: img/post-bg-sea4.jpg
catalog: true
tags:
    - Congestion Control
    - Distributed Systems
    - Machine Learning
    - Satellite Networking
---
 
### 1. Understanding Host Interconnect Congestion

揭示了Google实际生产集群中主机拥塞的原因：高带宽访问链路导致主机互连（NIC 到 CPU 数据路径）出现瓶颈。
>*Host congestion turns out to be a result of imperfect interaction (and resource imbalance!) between multiple components within the host interconnect*

<img width="450" height="450" src="/img/post-hotnets22-1-1.png"/>

接收端datapath
<img width="550" height="250" src="/img/post-hotnets22-1-2.png"/>

host congestion 两大核心原因：  
**IOMMU contention**:  
**Memory bus contention**:

>*Technology trends suggest that the problem of host congestion is only going to get worse with time. As discussed earlier, while host access link bandwidths are likely to increase by 10× over the next few years, technology trends for essentially all other host resources—e.g., NIC buffer sizes, the ratio of access link bandwidth to PCIe bandwidth, IOTLB sizes, memory access latencies, and memory bandwidth per core are largely stagnant.*

**解决思路**：Rethinking host architecture for future-generation datacenter networks; Rethinking congestion signals; Rethinking congestion response


### 2. Congestion Control in Machine Learning Clusters

这篇论文发现一个有意思的现象：传统拥塞控制追求的公平性对于ML工作负载可能不友好，不公平的CC反而可以加速ML工作负载

<img width="800" height="350" src="/img/post-hotnets22-2-1.png"/>


### 3. Sidecar: In-Network Performance Enhancements in the Age of Paranoid Transport Protocols


### 4. Towards Dual-band Reconfigurable Metamaterial Surfaces for Satellite Networking



### 参考文献

[Understanding Host Interconnect Congestion](https://conferences.sigcomm.org/hotnets/2022/papers/hotnets22_sagarwal.pdf)  
[Congestion Control in Machine Learning Clusters](https://people.csail.mit.edu/ghobadi/papers/unfairness_hotnets_2022.pdf)  
[Sidecar: In-Network Performance Enhancements in the Age of Paranoid Transport Protocols](https://conferences.sigcomm.org/hotnets/2022/papers/hotnets22_yuan.pdf)  
[Towards Dual-band Reconfigurable Metamaterial Surfaces for Satellite Networking](https://arxiv.org/pdf/2206.14939.pdf)
