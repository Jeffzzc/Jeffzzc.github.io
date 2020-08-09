---
layout:     post
title:      Aeolus SIGCOMM 2020
subtitle:   A Building Block for Proactive Transport in Datacenters
date:       2020-7-26
author:     Yiran
header-img: img/post-bg-field1.jpg
catalog: true
tags:
    - Congestion Control
    - Transport in Datacenter
---




Aeolus针对近年数据中心较热的主动拥塞控制协议一个普遍没有解决好的问题：第一个RTT内unscheduled的数据包直接发送造成丢包, 延时不可控。


- 主动拥塞控制思想：request and allocation， 显式分配带宽，提前避免拥塞发生
- 主动拥塞控制实现方式：基于集中控制器([Fastpass](http://fastpass.mit.edu/))、基于交换机([TFC](http://nns.cs.tsinghua.edu.cn/paper/eurosys16_jz.pdf) [PDQ](http://conferences.sigcomm.org/sigcomm/2012/paper/sigcomm/p127.pdf))、基于接收端([ExpressPass](http://ina.kaist.ac.kr/~dongsuh/paper/cho-sigcomm17.pdf) [pHost](https://conferences2.sigcomm.org/co-next/2015/img/papers/conext15-final1.pdf) [NDP](https://yi-ran.github.io/2019/06/11/NDP-SIGCOMM-2017/) [Homa](https://people.csail.mit.edu/alizadeh/papers/homa-sigcomm18.pdf))


### Motivation

- 第一个RTT传输很重要：随着带宽增长，越来越多的流能够在一个RTT内发送完毕；流可以从直接发送（不等待一个RTT后credit的分配结果）获益；空余带宽更多

<img width="500" height="400" src="/img/post-aeolus-1.png"/>

- 主动拥塞控制在第一个RRT内的问题

两个极端：

1. 如果第一个RTT所有流都先等待credit：带宽浪费，短流带来额外开销大
2. 如果第一个RTT所有流都线速发：充分利用带宽，但是无节制：```scheduled的数据包会受到unscheduled数据包的干扰```，尾延时增大。实验发现，
tails are due to buffer buildups and subsequent packet drops caused by senders bursting too many unscheduled packets in the first RTT.

<img width="500" height="400" src="/img/post-aeolus-3.png"/>


### Design

***Aeolus的设计目标***：希望unscheduled的数据包只充分利用网络剩余带宽而不干扰scheduled数据包，保证主动拥塞控制的带宽分配正常，同时具有良好的可部署性。

<img width="500" height="400" src="/img/post-aeolus-2.png"/>
  

### Thoughts




### 参考文献

[Aeolus A Building Block for Proactive Transport in Datacenters](http://www.cse.ust.hk/~kaichen/papers/aeolus-sigcomm20.pdf)





