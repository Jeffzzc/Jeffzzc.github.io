---
layout:     post
title:      ADS SIGCOMM CCR 2019
subtitle:   Datacenter Congestion Control Identifying what is essential and making it practical
date:       2020-7-29
author:     Yiran
header-img: img/post-bg-field2.jpg
catalog: true
tags:
    - Congestion Control
    - Transport in Datacenter
    - Flow Scheduling
---


### Core idea - two questions:

- What factors (i.e., which particular design decisions) are the most essential to achieving good performance?


- Can we deploy such designs easily?


### Key insights

- The most important factor in achieving good performance is using SRPT (or its close cousin, SJF) for packet scheduling. Our results
can be summarized in two statements: (1) A rate-setting scheme achieves its best performance when used with SRPT, and (2) with
SRPT, a wide range of rate-setting schemes achieve near-optimal performance.


- SRPT is neither necessary nor sufficient for good performance. Clearly, when combined with obviously inappropriate
rate-setting schemes (e.g., not increasing the window size sufficiently quickly when flows start, or having extremely long timeout
values), the resulting performance with SRPT can be significantly suboptimal; thus, SRPT is not sufficient. In addition, there are several congestion control proposals that mimic SRPT by coordinating host behavior. For instance, [pHost](https://conferences2.sigcomm.org/co-next/2015/img/papers/conext15-final1.pdf) approximates SRPT by precisely scheduling packet transmissions from the receivers. [Homa](https://people.csail.mit.edu/alizadeh/papers/homa-sigcomm18.pdf) adopts the same approach, and additionally, uses a few switch priority queues to improve the approximation accuracy for short flows. With careful tuning, these schemes can perform very well; thus, SRPT at switches is not necessary. However, our point is that with SRPT, near-optimal performance is easy to achieve.




### 参考文献

[Datacenter Congestion Control: Identifying what is essential and making it practical](https://ccronline.sigcomm.org/wp-content/uploads/2019/07/acmdl19-326.pdf)





