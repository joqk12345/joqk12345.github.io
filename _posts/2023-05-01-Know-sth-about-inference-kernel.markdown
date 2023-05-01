---
layout:     post
title:      "Inference Kernel"
subtitle:   "关于推理引擎的kernel介绍"
date:       2023-05-01 11:11:00
author:     "Galaxies"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - inference
    - kernel
    - High-performance
    - Neural Network Operators
    - Operator execution
    - Operator Scheduling
---


>
> 推理引擎的执行计算任务的最内核部分，类比于Linux操作系统的Kernel是完成神经网络推理任务的必备的、不可获取的组件，但往往却是个无名英雄，不过关注神经网络性能优化的话，主要看的就是这里。
> “Yeah It's on. ”
>  
>  

# Kernel优化架构介绍

推理引擎中的最最重要的一层，Kernel层承载了针对NPU、CPU、GPU等不同IP后端硬件的算子，或者Kernel实现。而 Kernel 层的优化主要有算法优化、内存优化和汇编优化三种，结合三种优化方式在真正运行之前会有调度优化。

1. Kernel在推理引擎的架构的位置
![](/img/in-post/post-ai/inference/kernel/arichitech.png)   

* 分类
  * 主要是包含人工高性能算子和高性能算子库
    * 人工算子是偏人工编写的
    * 高性能算子库是一些通用的算子聚合而成的
  * 按照设备可以分CPU/GPU/NPU

2. 整个推理流程
![](/img/in-post/post-ai/inference/kernel/inference-flow.png)   

3. 整个推理runtime的开发流程
![](/img/in-post/post-ai/inference/kernel/inference-runtime.png)   

下面我们来看具体的kernel优化篇

##  算法优化 

### 卷积

* 背景
卷积(Convolution, aka. Conv)，是神经网络的核心计算之一，它在 CV 领域方面的突破性进展 引领了深度学习的热潮。卷积的变种丰富，计算复杂，神经网络运行时大部分时间都耗费在计 算卷积，网络模型的发展在不断增加网络的深度，因此优化卷积计算就显得尤为重要。
随着技术的发展，研究人员提出了多种优化算法，包括 Im2col、Winograd 等等。
* Convolution
1. 在泛函分析中，卷积、旋积或褶积 (Convolution) 是通过两个函数f和g生成第三个函数的一种数 学运算，其本质是一种特殊的积分变换，表征函数 f 与 g 经过翻转和平移的重叠部分函数值乘 积对重叠长度的积分。
2. 卷积神经网络(Convolution Neural Networks, CNN)的概念拓展自信号处理领域的卷积。
3. 应用
   1. 我们能感知到的一大领域就是在Photoshop中滤镜，使用了不同的卷积策略(高斯卷积)。
4. 关于卷积的可视化网站
   1. [ conv_arithmetic ](https://github.com/vdumoulin/conv_arithmetic)
5. Convolution on Tensor
   1. 当中张量的内存布局为 NHWC 时，卷积计算相应的伪代码如下。其中外三层循环遍历输 出 C 的每个数据点，对于每个输出数据都需要经由内三层循环累加求和得到(点积)
   ![](/img/in-post/post-ai/inference/kernel/conv.png)   
   这就是为什么cnn计算比较慢的原因，无数硬件、软件算法都是为了通过各种手段提高卷积的计算性能；
6. 关于kenerl的调度优化
   1. 循环优化 （Loop Optimization）
      1. 循环展开(Loop Unrolling)
      2. 循环分块(loop tiling)
      3. 循环重排(loop Reorder) 
      4. 循环融合(loop Fusion)
      5. 循环拆分(loop Split)
   2. 指令优化（Instructions Optimization）
      1. 向量化 (Vector)
      2. 张量化(Tensor)
   3. 存储优化（Memory Optimization）
      1. 访存延迟( Latency Hiding )
      2. 存储分配(Memory Allocation)
7. 更多的优化策略可以学习AI编译器

### 卷积优化
1. 经典的Im2Col算法
   * 作为早期的深度学习框架，Caffe 中卷积的实现采用的是基于 im2col 的方法，至今仍是卷积重要的优化方法之一。
   * Im2col 是计算机视觉领域中将图片转换成矩阵的矩阵列(column)的计算过程。由于二维卷积 的计算比较复杂不易优化，因此在 AI 框架早期，Caffe 使用 Im2col 方法将三维张量转换为二维 矩阵，从而充分利用已经优化好的 GEMM 库来为各个平台加速卷积计算。最后，再将矩阵乘得 到的二维矩阵结果使用 Col2Im 将转换为三维矩阵输出。
   * ![](/img/in-post/post-ai/inference/kernel/img2col.png)   
   * Im2col+Matmul  算法过程
     * 使用 Im2col 将输入矩阵展开一个大矩阵，矩阵每一列表示卷积核需要的一个输入数据。
     * 使用上面转换的矩阵进行 Matmul 运算，得到的数据就是最终卷积计算的结果。
     * ![](/img/in-post/post-ai/inference/kernel/img2col-2.png)   

2. 整个算法的核心就是将tensor展开成为matrix，然后通过数据重排完成
   1. Tensor数据展开
   2. 数据重排
   
3. 优缺点分析
   1. Im2col 计算卷积使用 GEMM 的代价是额外的内存开销。使用 Im2col 将三维张量展开成二维矩 阵时，原本可以复用的数据平坦地分布到矩阵中，将输入数据复制了 KH∗KW−1 份。
   2. 转化成矩阵后，可以在连续内存和缓存上操作，而且有很多库提供了高效的实现方法(BLAS、 MKL)，Numpy 内部基于 MKL 实现运算的加速。
   3. 在实际实现时，离线转换模块实现的时候可以预先对权重数据执行 Im2col 操作，Input Data Im2col和GEMM的数据重排会同时进行，以节省运行时间。

4. 对应的空间组合优化算法（Spatial pack）
   1. 是一种基于分治法(Divide and Conquer)的方法，基于空间特性，将卷积 计算划分为若干份，分别处理。
   2. 划分后，大卷积计算被拆分为若干个小卷积进行计算。划分过程中计算总量不变，但计算小矩 阵时访存局部性更好，可以借由计算机存储层次结构获得性能提升:
   3. 实际应用
      1. 实际应用中可以拆为很多份。例如可以拆成小张量边长为 4 或者 8 ，从而方便编译器向量化计 算操作。随着拆分出的张量越小，Padding 引起的额外内存消耗越大，其局部性也越高，负面作 用是消耗的额外内存也越多。

5. 为什么这个是最重要的一层
   1. 推理引擎中的最最重要的一层，Kernel层承载了针对NPU、CPU、GPU等不同IP后端硬件的算子，或者Kernel实现。

### Winograd算法
1. Winograd算法，一种基于数值分析技术的快速矩阵运算算法,它可以有效地将矩阵乘法的计算量减少到非常小的数量！

2. 优缺点评价
   1. Winograd 计算单个小局部二维卷积的方法，这种算法不能将其直接应用在卷积网络的计算中 ，否则产生的辅助矩阵规模太大，会影响实际的效果;
   2. 不同规模的卷积需要不同规模的辅助矩阵，实时计算出这些辅助矩阵不现实，如果都存储起 来会导致规模膨胀。
   3. Winograd 算法通过减少乘法次数来实现提速，但是加法的数量会相应增加，同时需要额外的 转换计算以及存储转换矩阵，随着卷积核和分块的尺寸增大，就需要考虑加法、转换计算和 存储的代价，而且 tile 越大，转换矩阵越大，计算精度的损失会进一步增加，所以 Winograd 只适用于较小的卷积核和tile。

### QNNPack之间接优化算法
1. Quantized Neural Networks PACKage)
主要思路是使用空间换时间，解决矩阵乘的过程中的大量数据copy。
2. QNNPACK 做法将整个 K 维全部在计算 Kernel 中处理完，消除了输出部分和的访存。这里所 说的「将整个 K 维全部」并不是指 K 维不进行拆分，而是指拆分后不和其他维度交换，实际计 算中 K 维会以 2! 为基础进行拆分。
3. 间接卷积算法给出的答案是间接缓冲区(Indirect Buffer)，对内存重新组织(Repacking)可以 改进高速缓存命中率，从而提高性能。
4. 卷积之所以可以使用 Im2col 优化算法，本质原因在于其拆解后忽略内存复用后的计算过程等价 于矩阵乘。而间接缓冲区使得可以通过指针模拟出对输入的访存。
   1. prons
      1. 间接卷积优化算法解决了卷积计算的三个问题，1)是空间向量化问题，2)地址计算复杂问题 ，3)内存拷贝问题。
   2. cronos
      1. 通过间接卷积算法，建立的缓冲区和数据重新组织(Repacking)对内存造成大量的消耗。

### 内存的布局方式
1. 内存
   RAM 主内存是较大存储空间，但速度较慢。CPU/NPU Cache 缓存的速度要快得多，但其规模 较小，因此恰当地使用CPU/NPU Cache 缓存至关重要。
   每一次从 RAM 主内存中获取数据时，CPU/NPU 会将该数据及其邻近的数据加载到 Cache 缓存 中，以便利用访问局部性(locality of reference)提升访问命中率。
   * ![](/img/in-post/post-ai/inference/kernel/cpu-gpu-memeory.png)   
2. 内存对齐
3. Tensor 值存储在内存中
   1.  一个 Tensor 类的实例由一维连续的计算机内存段组成。结合Tensor 元素索引机制，可以将值 映射到内存块中对应元素的位置， 而索引可以变化的范围由 Tensor 的 Shape 属性指定。 每个 元素占用多少个字节以及如何解释这些字节由 Tensor 的 DataType 属性指定。
   2.  一个Tensor主要考虑的是他的shape、以及dataypte
4. Tensor的张量化表示
   1. ![](/img/in-post/post-ai/inference/kernel/tensor-represent.png)   

5. Tensor 值存储在内存中
   1. 一段内存本质上是连续的，有许多不同的方案可以将 N 维 Tensor 数组的项排列在一维块中。 根据排列顺序的区别，又可以分为行主序和列主序两种风格，下面以最简单的 2 维情况进行举 例:
   2. ![](/img/in-post/post-ai/inference/kernel/tensor-storage.png)

6. NCHW
   1. NCHW 同一个通道的数据值连续排布，更适合需要对每个通道单独运算的操作，如 MaxPooling • NCHW 计算时需要的存储更多，适合GPU运算，利用 GPU 内存带宽较大并且并行性强的特点，其访存与计算的控制逻辑相对简单

7. NHWC
   1. NHWC 其不同通道中的同一位置元素顺序存储，因此更适合那些需要对不同通道的同一数据 做某种运算的操作，比如“Conv1x1”
   2. NHWC 更适合多核CPU运算，CPU 内存带宽相对较小，每个数据计算的时延较低，临时空间 也很小，有时计算机采取异步的方式边读边算来减小访存时间，因此计算控制灵活且复杂

8. NCHWX
   1. NCHWX 的格式能够更好的适配SIMT，其中 NCHW4 可以针对 Arm int8 数据类型，利用 CUDA 的 dp4a 模块进行计算;
   2. 而 NCHW32 和 NCHW64 分别针对int8和int4数据类型，能够更好的利用 CUDA 的 Tensor core计算单元进行计算;
   3. 对 Cache 更友好，减少 Cache miss;Kernel 实现层面易进行 Padding，减少边界分支判断，代 码逻辑简单。

在深度学习AI训练框架中主要有两种内存排布方式,NCHW 和 NHWC。而在推理引擎里面，或者底层Kernel层实际上为了更加适配到DSA或者ASIC专用芯片会使用NCHWX内存排布格式

—— Galaxies

## 参考文献
1. >[ Convolution in Caffe: a memo ](https://github.com/Yangqing/caffe/wiki/Convolution-in-Caffe:-a-memo) 
2. >[ QNNPACK 实现揭秘](https://zhenhuaw.me/blog/2019/reveal-qnnpack-implementation.html) 
3. >[ data format ](https://oneapi-src.github.io/oneDNN/dev_guide_understanding_memory_formats.html)
4. >[ Optimize PyTorch Performance for Speed and Memory Efficiency  ](https://towardsdatascience.com/optimize-pytorch-performance-for-speed-and-memory-efficiency-2022-84f453916ea6)