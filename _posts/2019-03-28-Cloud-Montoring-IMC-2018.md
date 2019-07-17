---
layout:     post
title:      Cloud Monitoring IMC 2018
subtitle:   Experiences and Challenges
date:       2019-3-28
author:     Yiran
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - Measurement in Datacenter
---

### 核心思想

指出VNET的monitor和physical网路的monitor不同

### 提出三个问题
- 数据中心运营者能**monitor** VNET性能吗？之前那些用于物理网络的工具（[Pingmesh](https://yi-ran.github.io/2019/03/27/Pingmesh-SIGCOMM-2015/)，Everflow等）能够适用于VNET吗？
- 已有的monitor方法在测量VNET性能时准确度多高？能够**detect**出customer-impacting的问题吗？precision和recall如何？
- 除了monitor，VNET这种虚拟环境如何影响整个网络的**management**？

总体来讲，作者认为他们的VNET Pingmesh可用于VNET的monitor，但是租户网络和虚拟化给monitor带来了复杂性。


### Takeway

- cross-layer的aliasing使得monitor变得复杂。cross-layer interactions, ever-expanding network features.
- 检测到network overlay的failure后，定位原因和mitigation更难。customer applications和host management都会影响。mitigation：不能通过简单的reboot或者reroute（如果hypervisor出问题，不可能reroute）
- Baremetal monitoring alone does not account for VNET-specific performance anomalies

### VNET monitor的挑战（针对pingmesh）
- Black-box VM. 出于隐私考虑，不能在VM内运行任何软件或者收集数据。
- Avoiding customer impact. 探针不能影响customer，不能对customer计费、可见，也不能被customer伪造
- Interactions with customer rulesets. customer的支持Firewalls，gateway的ACL或者用户定义的routes等可能会干扰probe
- Interactions  with customer actions. 租户可能不可预测的关闭VM造成ping失败等等。如果不考虑这种用户行为会带来假阳性。

### VNET pingmesh做法

- 在VSwitch：发送端VFP注入基于TCP的ping. 位于租户的metering和ACL之后，接收端拦截ping并回响应。添加规则防止租户伪造ping避免干扰。————>解决了上面的挑战1 2 3 
待解决的事情：如何在VSwitch这一层区分出是人为关闭的VM还是failed的VM？

- **VNET的一个特点：比物理网络的节点数少！** 因此可以维护full mesh的Customer address->customer address的ping数据。Physical Pingmesh是从userspace(agent运行在user space)测量的latency，而VNET Pingmesh是从kernel（VFP在kernel）测量的，免去了上下文切换和调度时间。更加准确。

- 目前VNET pingmesh不能monitor routing tunnels。 

- **customer address与physical address的mapping会对测量的latency产生影响，特别是大的VNET**. When a VM starts a network flow to a given CA, the CA ⇒ PA mapping for the flow is queried from the userspace agent and cached in the kernel datapath; subsequent packets leverage this cache.————>large的VNET，地址转换容易cache miss
**passive的monitor是以后VNET monitor的趋势**

### 参考文献
[Cloud Datacenter SDN Monitoring: Experiences and Challenges](https://www.microsoft.com/en-us/research/uploads/prod/2018/09/CloudDatacenter.pdf)


