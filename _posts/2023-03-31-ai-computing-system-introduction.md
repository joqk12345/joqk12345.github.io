---
layout:       post
title:        "AI Computing System Intruction"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - computing
    - AI
    - gpt3
    - RL
 
---

# AI 计算体系与矩阵运算


## AI芯片指标

## 算力单位

![](/img/in-post/post-ai/hardware/Unit-of-Arithmetic.png)

1.  OPS
    1.  OPS
    2.  OPS/W
2. MACs
   1. Multiply-Accumulate Operations，乘加累计操作。1MACs包含一个乘法操作与一个加法操作， ~2FLOPs，通常MACs与FLOPs存在一个2倍的关系。

3. FLOPs
   1. Floating Point Operations，浮点运算次数，用来衡量模型计算复杂度，常用作神经网络模型速度 的间接衡量标准。对于卷积层而言，FLOPs的计算公式如下: 𝐹𝐿𝑂𝑃𝑠=2(𝐻(𝑊(𝐶#$ (𝐾(𝐾(𝐶%&'

4. Memory Access Cost
   1. Memory Access Cost，内存占用量，用来评价模型在运行时的内存占用情况。 1𝑥1 卷积FLOPs 为 2 ⋅ 𝐻 ⋅ 𝑊 ⋅ 𝐶!" ( 𝐶#$% , 其对应MAC为:
𝐻(𝑊( 𝐶in +Cout' +(𝐶in ∗𝐶out')


## Key Metrics – AI芯片关键指标



## 矩阵运算








## 比特位宽


## 参考文件

1.  > [ GPT-3 + RL 全流程训练开源整理]
   (https://zhuanlan.zhihu.com/p/608705255) 


   



