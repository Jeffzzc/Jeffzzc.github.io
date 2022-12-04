---
layout:     post
title:      Aequitas SIGCOMM 2022
subtitle:   Admission Control for Performance-Critical RPCs in Datacenters
date:       2022-09-13
author:     Yiran
header-img: img/post-bg-sky3.jpg
catalog: true
tags:
    - microsecond-scale
    - Distributed Systems
---
 
分布式Admission Control，保证性能敏感**RPC**的**网络时延**满足SLO

### Background and Motivation

**三类RPC**:  
(1) Performance-critical (***PC***) RPCs have tail latency SLOs. Sometimes they are associated with real-time interactive applications or carry key control traffic.  
(2) Non-critical (***NC***) RPCs: generally care about sustained rate and their latency SLOs are less stringent on the tail relative to PC RPCs.  
(3) Best-effort (***BE***) RPCs have the lowest priority, such as background backup traffic which sees no imminent disadvantage to elevated latency as long as it eventually completes. BE RPCs have no SLOs and are akin to a scavenger class 

RPC network-latency (***RNL***) : the time between the first RPC packet arriving at the transport layer (such as TCP) and the time when the last packet of the RPC is acknowledged at the transport.   

**Network overload**: overloads can occur anywhere in the network along the path that an RPC takes between the client and the server. 

**Mitigating network impact**: 现有实现性能隔离的方案：  
（1）基于流大小调度：size与RPC真实性能敏感度并不对应，不一定小流优先级应当设置为高  
（2）严格优先级：应用级别，粒度粗，没有SLO保证，饿死问题；race to top问题, 大家都设置高优先级  
（3）加权公平队列：应用级别，粒度粗，没有SLO保证；race to top问题, 大家都设置高优先级  

### Aequitas

Goal: provide RNL SLOs for RPC priority classes with performance requirements (PC and NC). 

面临的挑战:  
(1) Expressing SLOs for a diverse set of applications. Latency degradation of RPCs can result from an overload of compute, storage, or networking. It is key to tease out an SLO that the network can be held accountable for.  
(2) Structural. Given that overloads can occur anywhere in the network, the solution needs to handle dynamic overloads appearing anywhere along the path an RPC traverses.  
(3) Scale. There may be tens of thousands of hosts, thousands of tenants, and hundreds of applications in a cluster, all requiring RPC performance at microsecond-scale.

<img width="550" height="550" src="/img/post-aequitas-1.png"/>

**key idea**: By comparing the RNL SLO targets and the actual measurements, the algorithm adjusts the amount of traffic admitted per destination-host for the QoS at which the RPC ran.


**理论分析（具体参见论文）**：controlling RPC network-latency across priority classes to provide differentiated SLOs by controlling the amount of traffic admitted on the respective QoS as realized by WFQ.

**两个核心设计**:  
***Align Network QoS with RPC priority***: Aequitas maps, at the granularity of RPCs, the three priority classes bijectively to three QoS classes served with WFQ-scheduling: PC RPCs to QoSh, NC to QoSm, and BE to QoSl. Aequitas provides SLOs for QoSh andQoSm;QoSl is treated as a scavenger class on which best-effort and downgraded traffic is served and offers no SLOs.  
***Distributed Admission Control via QoS downgrade to provide RNL SLOs***: whether to admit a given RPC on the requested QoS by controlling an admit probability. This controls the portion of RPCs admitted across QoS levels in order to meet RNL SLOs. In a departure from traditional mechanisms of admission-control that either drop or rate- limit traffic, Aequitas downgrades the unadmitted RPCs and issues them at the lowest QoS level. The algorithm follows an Additive Increase Multiplicative Decrease (AIMD) control.
<img width="430" height="900" src="/img/post-aequitas-2.png"/>


### Evaluation

[部分仿真代码](https://github.com/SymbioticLab/Aequitas)

QoS-mix(QoSh , QoSm , QoSl ) is (0.6, 0.3, 0.1) 
<img width="500" height="400" src="/img/post-aequitas-3.png"/>

<img width="500" height="400" src="/img/post-aequitas-4.png"/>

<img width="550" height="450" src="/img/post-aequitas-5.png"/>

<img width="550" height="450" src="/img/post-aequitas-6.png"/>


### Thinking


### 参考文献

[Aequitas: Admission Control for Performance-Critical RPCs in Datacenters](https://symbioticlab.org/publications/files/aequitas:sigcomm22/aequitas-sigcomm22.pdf)
