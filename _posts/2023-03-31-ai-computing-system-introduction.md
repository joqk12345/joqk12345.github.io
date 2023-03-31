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
   1. Floating Point Operations，浮点运算次数，用来衡量模型计算复杂度，常用作神经网络模型速度 的间接衡量标准。
   2. FLOPs = 每秒浮点运算次数 × 浮点运算的操作数
   其中，每秒浮点运算次数表示计算机每秒钟可以执行的浮点运算次数，浮点运算的操作数表示单个浮点运算所需要的基本操作数。
       * 假设一台计算机每秒钟可以执行100亿次浮点运算（即10^10 FLOPs），而单个浮点运算需要执行5个基本操作（如加、减、乘、除等），则该计算机的每秒钟的FLOPs数为：
      FLOPs = 10^10 × 5 = 50亿
      * 对于卷积层而言，FLOPs的计算公式如下: 𝐹𝐿𝑂𝑃𝑠=2(𝐻(𝑊(𝐶#$ (𝐾(𝐾(𝐶%&'
   
4. Memory Access Cost
   1. Memory Access Cost，内存占用量，用来评价模型在运行时的内存占用情况。 1𝑥1 卷积FLOPs 为 2 ⋅ 𝐻 ⋅ 𝑊 ⋅ 𝐶!" ( 𝐶#$% , 其对应MAC为:
𝐻(𝑊( 𝐶in +Cout' +(𝐶in ∗𝐶out')


## Key Metrics – AI芯片关键指标
1. 精度 Accuracy
   * 计算精度 (FP32/FP16 etc.) 
   * 模型结果精度 (ImageNet 78%)
2. 吞吐量 Throughoutput
   * 高维张量处理 (high dimension tensor) 
   * 实时性能 (30 fps or 20 tokens)
3. 时延 Latency 
4. 能耗 Energy
   1. IOT 设备有限的电池容量
   2. 数据中心液冷等大能耗
5. 系统价格 System Cost
   1. 硬件自身的价格 
      1. 片内多级缓存 Cache 大小 >> 内存设计
      2. PE 数量、芯片大小、纳米制程 >> 电路设计
   2. 系统集成上下游全栈等成本
6. 易用性 Flexibility
   1. 衡量开发效率和开发难度
   2. 对主流AI框架支持度 (PyTorch) >> 软件栈

## 对于AI加速的关键设计点
1. 提升吞吐量 Increase Throughput 和降低延时 Reduce Latency
2. 低延时 Low Latency 和 Batch Size 之间 Tradeoff
3. 硬件调度思考
   1. 去掉没有意义的 MACs
      1. 增加对稀疏数据的硬件结构 sparse data
   2. 降低每次 MAC 的计算时间
      1. 增加时钟频率 clock frequency
      2. 减少指令开销 instruction overhead
4. PE, Processing Elements
   1. 增加 PE 的核心数量
      1. 更多的MACs并发(parallel)
      2. 使用更高纳米制程，增加PE的面积密度(areadensity)
   2. 增加 PE 的利用率 utilization
      1.  将计算负载尽可能分配到不同PE(distributeworkload)
      2.  均衡PE之间计算负载(balanceworkloads)
      3.  合适的内存带宽有效降低空闲时钟周期(reduceidlecycles)
![](/img/in-post/post-ai/hardware/process-elelment.png)


## 计算性能仿真

1. 需要考虑的就是数据带宽与计算带宽的比例。

![](/img/in-post/post-ai/hardware/Computing%20performance%20simulation.png)

详细可以参考，参考文献。


## 矩阵运算(Matrix Multiplication)

1.  从卷积 Conv 到矩阵乘 MM
  * 通过数据重排，完成 Im2col 的操作之后会得到一个输入矩阵，卷积的 Weights 也可以转换为一
 个矩阵，卷积的计算就可以转换为两个矩阵相乘的求解，得到最终的卷积计算结果。
![](/img/in-post/post-ai/hardware/conv-to-mm-1.png)

     * 在计算 𝑀𝑅×𝑁𝑅 小块时，传统的方法是在 K 维度上拆分，在一次计算 Kernel 处理中，仅计 算 K 维的局部。那么在每次计算 Kernel 的处理中，都会发生对输出的加载和存储。

   ![](/img/in-post/post-ai/hardware/conv-to-mm.png)

   * 矩阵乘分块 Tiling
  根据 Cache 大小来对矩阵进行分块 Tiling，最大程度重用数据和利用空间换时间


2. CPU/GPU 支持矩阵乘的库
   * 实现逻辑:
      * Lib 感知相乘矩阵的 Shape
      * 选择最优的 Kernel 实现来执行
   * 实现方法
     * Loop 循环优化 (Loop tiling)
     * 多级缓存 (memory hierarchy)
   * 现有库（Matrix Multiplication (GEMM)
     * CPU: OpenBLAS, Intel MKL, etc.
     * GPU: cuBLAS, cuDNN, etc.

![](/img/in-post/post-ai/hardware/implement-mm.png)

3. 卷积代替算法
   1. Fast Fourier Transform
   2. Strassen
   3. Winograd 

## 比特位宽(Bits Width)
1. 量化原理
   1. 模型量化桥接了定点与浮点，建立了一种有效的数据映射关系，使得以较小的精度损失代价获 得了较好的收益

2. bit Witdh 是什么
* Floating Point(FP): allows range to change for each value(E-bits)
* Fixed Point(Int): has fixed range

![](/img/in-post/post-ai/hardware/bit-width.png)

* CPU 或者 GPU 默认采用 FP32
   
1. 降低精度的好处
   1. 对于 MAC 的输入和输出，能够有效减少数据的搬运和存储开销
      1. SmallerMemory->Lowerenergy
   2. 减少 MAC 计算的开销和代价
      1.  int8 x int8 -> int16;fp16 x fp16 -> fp32
   3. 降低位宽对功耗和芯片面积的影响

2. 什么决定比特位宽 Bit Width ?
   1. 训练 Training: weights, activation, partial sums,gradients and weight update.
   2. 推理 Inference: weights, activations, partial sums

3. 做法
   1. 对精度的影响 Impact on Accuracy
      1.  需要考虑不同数据集(NLP/CV)、不同任务
      2.  不同网络模型之间的差异进行测评 (e.g., classification > detection)
   2. 训练和推理的数据位宽
      1. 32bit float 可以作为弱基线;
      2. 对于训练使用 FP16、BF16、TF32;
      3. 推理 CV 任务以 int8 为主，NLP 以 FP16为主，大模型 int8/FP16 混合;


## 参考文件

1.  > [ Eyeriss v2: A Flexible Accelerator for Emerging Deep Neural Networks on Mobile Devices ]
   (https://arxiv.org/pdf/1807.07928.pdf) 

2. > [ DeepLearningSystem]
   (https://github.com/chenzomi12/DeepLearningSystem) 
   



