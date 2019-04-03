---
layout:     post
title:      NetBouncer NSDI 2019
subtitle:   Failure Localization in Data Center Networks
date:       2019-3-28
author:     Yiran
header-img: img/post-bg-kuaidi.jpg
catalog: true
tags:
    - Machine Learning
    - Network Troubleshooting
---

## [NetBouncer: Active Device and Link Failure Localization in Data Center Networks](https://www.usenix.org/system/files/nsdi19-tan.pdf)

### 核心思想

通过IP-in-IP 的probe探测，定位数据中心故障，包括device故障和link故障。（微软）

### Network Troubleshooting三个requirement(Motivation)

- End host’s perspective: 交换机无法观察到gray failure. gray failure: 交换机不会记录. e.g. drop packets probabilistically, and hence cannot be detected by simply evaluating connectivity.
- Compatible with commodity hardware, existing network protocols and software stack 
- Precise and accurate: [Pingmesh](https://yi-ran.github.io/2019/03/27/Pingmesh-SIGCOMM-2015/)等工具只能定位region.


### Design Overview

<img width="450" height="450" src="/img/post-netbouncer-1.png"/>

#### 1.Probing plan design

   模型：每一个link都有个成功概率，一条路径上link的成功概率之积就是路径成功概率.

   假设：failures are **independent**

   目标：link-identifiable 的 probe plan

   ![](/img/post-netbouncer-3.png)

   **Theorem 1. (sufficient probing theorem 证明见附录)**. In a Clos network with k layers of switches (k ≥ 1), by probing all paths from the servers to the top-layer switches, we can uniquely infer the link success probabilities from the measured path success probabilities, if and only if at least one path with success probability 1 passes each switch

   注意：Netbouncer与其他最小化probe数量的工作不同。Netbouncer不以最小化probe数量作为目标。


#### 2.Efficient path probing via IP-in-IP

   <img width="450" height="250" src="/img/post-netbouncer-2.png"/>

   Probing采用一种IP-in-IP的packet bouncing的方式。以前的方案中ping-based的probing is unable to pinpoint the routing path; Tracert consumes switch CPUs. switch CPU的资源消耗有限制（[007](https://www.usenix.org/system/files/conference/nsdi18/nsdi18-arzani.pdf)）。

   ```Packet bouncing```: 发probe的server选定一个交换机，交换机会将probe弹回。sender和receiver是同一个交换机。链路被双向探测。
   

#### 3.Failure inference from path measurements

   大致思路：定理一提供了一个能够解出唯一解的充要条件（每个交换机至少有一条成功概率为1的路径经过）。通过这个条件，可以将clos网络分为可解部分和非可解部分。可解部分可通过阈值筛选出故障的链路，非可解部分则认为是交换机故障。

   ***问题：how can one infer the link probabilities from the end-to-end path measurements?***

   **挑战**：数据不一致。包括不精准的测量引起overfit、意外的数据丢包。大部分会引起假阳性问题（把一个正常链路视作问题链路）

   **[latent factor model](https://www.jianshu.com/p/f10d15ac6a80) 结合troubleshooting领域知识**：

   ![](/img/post-netbouncer-4.png)

   第二项是一个特殊的正则化。有两个特性：```(a), it has a two-direction penalty; (b), because of the quadratic term, the closer to 1 the greater the slope.``` 

   特性a：The regularization term tends to “pull” the good links to be better, while “push” the bad links to be worse, while the product of link probabilities will stay approximately the same

   特性b: The characteristic (b) mitigates the accidental packet loss and noisy measurements, which helps endorse most links (good) and assign the blame to only a small number of links (bad)

   模型使用Non-convex representation （比convex representation效果好），求解方法：coordinate descent（[坐标下降法](https://zhuanlan.zhihu.com/p/52300427)),不用梯度下降的原因是运行时间慢

   <img width="350" height="850" src="/img/post-netbouncer-5.png"/>


### Implementation and evaluation

- 仿真：与hop-by-hop比较性能非常接近，device failure detection对避免假阴性必不可少，特殊正则化vs standard正则化，随机梯度下降（SGD）vs 坐标下降（CD）收敛时间，正则化参数lamda对假阳性假阴性的影响，与已有系统比较： [deTector](https://www.usenix.org/system/files/conference/atc17/atc17-peng.pdf), NetScope, and KDD14

   <img width="450" height="450" src="/img/post-netbouncer-6.png"/>

- Testbed： controller、agent 架构与Pingmesh类似

   NetBouncer chooses 5  minutes as one probing epoch. 算法运行时间在几十秒左右（60秒以内）

   列举了几个真实发现的问题，详细戳论文标题链接

### NetBouncer的局限
- 假设所有probing packets 会经历与应用数据一样的failures
- 针对某些特定数据包的错误 如packet black hole，ACL或防火墙误配置等 无法定位，因为NetBouncer scan的是大量的IP地址
- 理论上不能保证零假阳性和零假阴性
