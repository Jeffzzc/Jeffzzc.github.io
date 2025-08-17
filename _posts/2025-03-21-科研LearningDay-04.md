---
layout:     post
title:      科研Day-03
subtitle:   GPT简要实现
date:       2025-4-20
author:     Jeffzzc
header-img: img/home-bg-sky2.jpg
catalog: true
tags:
    - 科研
---
### GPT

#### GPT简介

    GPT(Generative Pre-trained Transformer)基于Transformer解码器自回归地预测下一个Token，从而进行了语言模型的建模。显然这里的GPT是基于transformer的一个Decoder-based模型。

    GPT适用于生成任务（自回归语言模型，任务更难但潜力更大）， BERT适合判别。

    GPT的输入是一些由整数表示的文本序列，每个整数都与文本中的token对应，而输出则是一个二维数组，其中**output[i][j]** 表示文本序列的第i个位置的token（inputs[i]）是词汇表的第j个token（vocab[j]）的概率（实际为未归一化的logits得分）。为预测序列的下一个token，只需在output的最后一个位置中选择可能性最高的token。那么，通过迭代地将上一轮的输出拼接到输入，并送入模型，从而持续地生成token。这种生成方式称为贪心采样。实际可以对类别分布用温度系数T进行蒸馏（放大或减小分布的不确定性），并截断类别分布的按top-k，再进行类别分布采样。
