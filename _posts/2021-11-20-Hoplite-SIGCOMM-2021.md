---
layout:     post
title:      Hoplite SIGCOMM 2021
subtitle:   Efficient and Fault-Tolerant Collective Communication for Task-Based Distributed Systems
date:       2021-11-20
author:     Yiran
header-img: img/post-bg-bear.jpg
catalog: true
tags:
    - Distributed Sytems
---

(未完待续)

### Background

- Task-based distributed systems
  
  asynchronous and dynamic computation: a caller can dynamically invoke a task 𝐴, which immediately returns an object future, i.e. a reference to the eventual return value. By passing the future as an argument, the caller can specify another task 𝐵 that uses the return value of 𝐴 even before 𝐴 finishes. The task-based system is responsible for scheduling workers to execute tasks 𝐴 and 𝐵 and transferring the result of 𝐴 to 𝐵 between the corresponding workers.

  <img width="450" height="800" src="/img/post-hoplite-0.png"/>


- Collective Communications

  Collective communication: e.g., **broadcast, reduce**

  现有的collective communication library: OpenMPI, MPICH, Horovod, Gloo, and NCCL. 


### Motivation

- 现有的collective communication library的问题
  
  1. The communication pattern has to be **statically** defined before runtime.  只对具有bulk-synchronous parallel 模型的应用友好。例如：all the workers compute on their partitioned set of training data and synchronize the model parameters using allreduce.

  2. 容错性不佳. When any worker fails, all the workers participating in the collective communication hang, and applications are responsible for fault tolerance: checkpointing the entire application periodically (e.g., per-hour), and when a process fails, the entire application rolls back to a checkpoint and re-execute.


**Goal: Bring the efficiency of collective communication to dynamic and asynchronous task-based applications**.

 - ```A collective communication layer for a task-based system should adjust data transfer schedule at runtime based on task and object arrivals.```

 - ```A collective communication layer for task-based systems has to be fault-tolerant: allowing well-behaving tasks to make progress when a task fails and allowing the failed task to rejoin the collective communication after recovery.```




### Design

核心设计两点：

- decentralized fault-tolerant coordination of data transfer for reduce and broadcast 
- pipelining of object transfers both across nodes and between tasks and the object store.

**Hoplite Workflow**

**Object Directory Service**

**Pipelining**

**Receiver-Driven Collective Communication**

  <img width="950" height="650" src="/img/post-hoplite-2.png"/>

**fault-Tolerant Collective Communication**


### Implementation



### Evaluation

<img width="1100" height="850" src="/img/post-hoplite-3.png"/>


### Thinking


### 参考文献

[Hoplite: Efficient and Fault-Tolerant Collective Communication for Task-Based Distributed Systems](https://dl.acm.org/doi/10.1145/3452296.3472897)
