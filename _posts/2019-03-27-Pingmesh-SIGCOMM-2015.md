---
layout:     post
title:      Pingmesh SIGCOMM 2015
subtitle:   Data Center Network Latency Measurement and Analysis
date:       2019-3-27
author:     Yiran
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
    - Measurement in Datacenter
    - Network Troubleshooting
---

### 核心思想

构建一个大规模的数据中心latency测量和分析系统（微软）

### 网络运维面临的挑战

确定是否是网络的问题、定义和追踪SLA、network troubleshooting


### Design
<img width="450" height="450" src="/img/post-pingmesh-1.png"/>

- **Pingmesh Controller**: 负责产生pinglist.为了减少probe数目, 设计了多重complete gragh. 分为inter-dc和intra-dc. intra-dc:一个ToR switch看做是一个点. 两个ToR switch: 相同位置的server给相同位置的server发probe. inter-dc:  一个datacenter看做是一个点.
- **Pingmesh Agent**: 向controller pull pinglist，TCP/HTTP ping, 上传结果. Every probing needs to be a new connection and uses a new TCP source port. Agent有很多实现上性能和安全的考虑: 严格控制CPU overhead、memory overhead、probe间隔固定为10s. payload 64 bytes.
- **Data Storage and Analysis**: 负责分析 产生报告 可视化等

-***Thinking：***  Pingmesh采取的是测量全部pair的RTT,RTT是相对值,因此不需要像[SIMON](https://yi-ran.github.io/2019/03/27/SIMON-NSDI-2019/)一样做到端网卡上的时钟同步

-***Pingmesh is Always On***


### Implementation and evaluation
- **测量网络的latency和packet drop rate**
   ![](/img/post-pingmesh-2.png)
- **Network SLA 定义 ：packet drop rate, network latency at 50th and 99th percentile**
- **Packet balck-hole detection:** 定位算法：If many servers under a ToR switch experience the black-hole symptom, then we mark the ToR switch as a black-hole candidate and assign it a score which is the ratio of servers with black-hole symptom. We then select the switches with black-hole score larger than a threshold as the candidates. Within a podset, if only part of the ToRs experience the black-hole symptom, then those ToRs are blacking hole packets. If all the ToRs in a podset experience the black-hole symptom, then the problem may be in the Leaf or Spine layer. 一般可通过reload switch解决
   <img width="450" height="450" src="/img/post-pingmesh-3.png"/>
- **Packet slient drop detection:** *正常情况下 丢包率为 $ 10^{-4}-10^{-5} $,* 突然发现丢包率上升到 $ 2\*10^{-3} $.但是没有丢包迹象。Using Pingmesh, we could figure out several source and destination pairs that experienced around 1%-2% random packet drops. We then launched TCP traceroute against those pairs, and finally pinpointed one Spine switch.
   <img width="450" height="450" src="/img/post-pingmesh-4.png"/> 
   **Silent packet drops 的原因**：switching fabric CRC checksum error, switching ASIC deficit, linecard not well seated, etc. 不能通过reload解决。

### Pingmesh局限
- 只知道有failure，无法准确定位是哪个设备：例如Packet slient drop具体是那个交换机故障了 还需要结合traceroute
- 只测量Single packet RTT：而有的failure场景需要多个packet连续的RTT测量。例如TCP parameter tuning错误：the initial congestion window (ICW) reduced from 16 to 4. For long distance TCP sessions, the session finish time increased by several hundreds of milliseconds if the sessions need multiple round trips. Pingmesh did not catch this because it only measures single packet RTT.

### 参考文献
[Pingmesh: A Large-Scale System for Data Center Network Latency Measurement and Analysis](https://conferences.sigcomm.org/sigcomm/2015/pdf/papers/p139.pdf)