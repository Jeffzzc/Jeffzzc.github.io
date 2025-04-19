---
layout:     post
title:      科研Day-02
subtitle:   LoRA
date:       2025-4-19
author:     Jeffzzc
header-img: img/home-bg-sky2.jpg
catalog: true
tags:
    - 科研
---
### LoRA

#### LoRA定义

    LoRA: Low-Rank Adaptation 论文里面的介绍是这样的：LoRA allows us to train some dense layers in a neural network indirectly by optimizing rank decomposition matrices of the dense layers’ change during adaptation instead, while keeping the pre-trained weights frozen.

- LoRA 有很多的优点，节约显存，训练快，效果损失较小（相对于全参数微调），推理的时候不增加耗时，可以做一个插入式组件使用。缺点当然也有，那就是还是会有一些效果的损失。

#### ******LoRA的原理、流程及架构******

- 核心原理非常的简单，任意一个矩阵 $W_0$，都可以对它进行低秩分解，把一个很大的矩阵拆分成两个小矩矩阵 $A B$，在训练的过程中不去改变 $W_0$ 参数，而是去改变 $AB$。具体可以表示为$W_{new}=W_0+AB$  这里的AB的秩远小于W。

### 前置知识补充

#### 什么是Kaiming初始化

    Kaiming初始化（也称为He初始化）是一种神经网络权重初始化方法，由何恺明（Kaiming He）等人提出，旨在解决深度网络训练中的梯度消失或爆炸问题，特别是在使用ReLU等激活函数时。

Kaiming初始化通过设置权重的初始值，使每一层的输出方差尽量保持一致，避免随着网络深度的增加，梯度变得过大或过小。它基于以下假设：

* 权重初始化的方差应与输入神经元数量（或扇入，fan_in）成反比。
* 激活函数（如ReLU）会导致部分输出为0，影响方差。

#### PyTorch实现

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import math

class LinearLoRALayer(nn.Module):
    def __init__(self, 
                 in_features,
                 out_features,
                 rank,
                 lora_alpha,
                 dropout,
                 merge=False): 
        super().__init__()
     
        self.in_features = in_features
        self.out_features = out_features
        self.rank = rank
        self.lora_alpha = lora_alpha
        self.dropout = dropout
        self.merge = merge
    
        self.linear = nn.Linear(in_features, out_features)
        # Input shape 是 (batch_size, sequence_length, in_features)
        # 计算过程是 x @ weight.T
        # 所以 weight 的 shape 是 (out_features, in_features)
        # 这里的 A @ B = Weight 的权重
    
        if rank > 0:
            self.lora_a = nn.Parameter(torch.randn(out_features, rank)) 
            # 是一个高斯分布
            nn.init.kaiming_uniform_(self.lora_a, a=math.sqrt(5))
        
            self.lora_b = nn.Parameter(torch.randn(rank, in_features))
            self.scale = lora_alpha / rank
            # 这里的 scale 是用来缩放矩阵数值的
        
        self.dropout = nn.Dropout(dropout) if dropout > 0 else nn.Identity()
    
        # merge 是用来控制是否合并的
        if merge:
            self.merge_weight()
    
    def merge_weight(self, ):
        if self.merge and self.rank > 0:
            # (out_features, rank) @ (rank, in_features) = (out_features, in_features)
            self.linear.weight.data += self.scale * (self.lora_a @ self.lora_b)
  
    def unmerge_weight(self, ):
        if self.merge and self.rank > 0:
            # (out_features, rank) @ (rank, in_features) = (out_features, in_features)
            self.linear.weight.data -= self.scale * (self.lora_a @ self.lora_b)
        
    def forward(self, X):
        # X shape 是 (batch_size, sequence_length, in_features)
    
        if rank > 0:
            output_part1 = self.linear(X)
            output_part2 = self.scale * (X @ (self.lora_a @ self.lora_b).T)
            output = output_part1 + output_part2
        else:
            output = self.linear(X)
    
        output = self.dropout(output)
        return output

# 写一段测试代码
# Test the LoRALinear layer
batch_size = 32
seq_len = 128
in_features = 768
out_features = 512
rank = 8
lora_alpha = 16
dropout = 0.1


# Create a test input
x = torch.randn(batch_size, seq_len, in_features)

# Test regular mode (no merge)
lora_layer = LinearLoRALayer(
    in_features=in_features,
    out_features=out_features,
    rank=rank,
    lora_alpha=lora_alpha,
    dropout=dropout,
    merge=False
)

# Forward pass
output = lora_layer(x)
print(f"Output shape (no merge): {output.shape}")  # Should be [batch_size, seq_len, out_features]

# Test merged mode
lora_layer_merged = LinearLoRALayer(
    in_features=in_features,
    out_features=out_features,
    rank=rank,
    lora_alpha=lora_alpha,
    dropout=dropout,
    merge=True
)

# Forward pass with merged weights
output_merged = lora_layer_merged(x)
print(f"Output shape (merged): {output_merged.shape}")  # Should be [batch_size, seq_len, out_features]

# Test weight merging/unmerging
lora_layer.merge_weight()
output_after_merge = lora_layer(x)
lora_layer.unmerge_weight()
output_after_unmerge = lora_layer(x)

print("Max difference after merge/unmerge cycle:", 
      torch.max(torch.abs(output - output_after_unmerge)).item())
```
