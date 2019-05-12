---
layout:     post
title:      LossRadar CoNEXT 2016
subtitle:   Fast Detection of Lost Packets in DC
date:       2019-5-11
author:     Yiran
header-img: img/post-bg-forest.jpg
catalog: true
tags:
    - Network Troubleshooting
---

## [LossRadar: Fast Detection of Lost Packets in Data Center Networks](http://minlanyu.seas.harvard.edu/writeup/conext16.pdf)
### 核心思想 

专门用于快速检测丢包以及获取丢包详细信息的monitor system. 可以说是第一篇**专门**做数据中心丢包troubleshooting的文章，[007](https://www.usenix.org/system/files/conference/nsdi18/nsdi18-arzani.pdf)在此文章之后.

### Packet Loss Background

1. 丢包很常见并且对数据中心应用影响大：on average in a production data center for one year, up to 40-80 machines can experience packet losses, 4 network maintenance jobs can cause 30-minute random connectivity losses, and 3 router failures can cause immediate traffic blackholing for an hour. 数据中心常见丢包分类：

   - **拥塞丢包**
   - **黑洞（持续性、暂态）**：匹配到某个特定'pattern'的数据包被丢弃. 持续性黑洞一般是由于match-action table corruption或控制器规则配置错误引起, 暂态黑洞一般是由于非原子的规则更新或者网络范围内更新不一致引起.
   - **随机丢包**：交换机随机丢弃数据包without report. 可能是由于有故障的linecard或者link.


2. 丢包检测的需求：

   - **Fast detection**: 只有快速检测才能更快narrow down root cause 和诊断. 减少丢包影响.
   - **Generic detection of all loss types**: 丢包原因很多
   - **Capturing location information**: 知道具体位置才能快速采取action, 交换机 link 还是 host NIC
   - **Capturing packet header information**: 需要区分丢包原因采取针对性措施，因此很多详细信息必不可少：五元组，timing，loss pattern等.

3. 已有方案的不足：

   - **End host based solutions**: 准确定位丢包位置困难. Pingmesh可以推断丢包位置，但是不能与应用关联，不能区分丢包原因，并且probe的频率太低. ([007](https://www.usenix.org/system/files/conference/nsdi18/nsdi18-arzani.pdf)发表于这片文章之后，已经能够定位link，与应用关联，但是不能区分丢包原因)
   - **Packet mirroring at switches**: 漏掉丢包类型和数量
   - **Counters at switches**: 因为丢包原因多种并且不确定，很难提前配置好对应的counter. [FlowRadar](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-li-yuliang.pdf)（NSDI 2016）使用per-flow的counter，但是需要多交换机的counter对比同步才能知道丢包，并且存储消耗与monitor的流数目成正比.


### Design Overview

设计分别针对丢包检测的需求.

- **Generic to all types of losses**：

- **Fast detection of losses and their locations**：

- **Capture packet header information with low overhead**：


### Challenges

- **Small size digests**:
- **Meter placement**:
- **Loss analyzer**:



### Network Wide Deployment

### Loss Analyzer


### Implementation and evaluation




