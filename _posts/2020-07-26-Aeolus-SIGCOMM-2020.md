---
layout:     post
title:      Aeolus SIGCOMM 2020
subtitle:   Aeolus A Building Block for Proactive Transport in Datacenters
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

- 第一个RTT传输很重要：随着带宽增长，越来越多的流在一个RTT内发送完毕

<img width="550" height="450" src="/img/post-aeolus-1.png"/>

- 主动拥塞控制在第一个RRT内的问题


### Design

  

### Thoughts




### 参考文献

[Aeolus A Building Block for Proactive Transport in Datacenters](http://www.cse.ust.hk/~kaichen/papers/aeolus-sigcomm20.pdf)





