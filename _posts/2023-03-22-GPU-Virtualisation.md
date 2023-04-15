---
layout:       post
title:        "GPU Virtualisation of  basic GPU concept"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - gpu
    - deepl
    - DeepLearning
    - virtulisation
---

# GPU的学习入门笔记

最近在做GPU相关的推理优化，利用率的提升，整理了一些GPU相关的基础概念。

## GPU

GPU，全称为Graphics Processing Unit，即图形处理器，是一种专门用于加速计算的处理器。与传统的中央处理器（CPU）相比，GPU在并行处理能力上更加强大，能够同时处理大量的数据，并在极短的时间内完成复杂的计算任务。

GPU最初是为了处理计算机图形和图像而设计的，但随着计算需求的增加，GPU也逐渐被应用于各种科学计算、数据处理、机器学习、深度学习和人工智能等领域。由于GPU的优异性能和高效能力，它已成为计算机行业的重要组成部分，受到越来越广泛的关注和应用。

在计算机中，GPU通常是作为独立的硬件设备存在，可以通过PCIe接口与CPU连接。在GPU内部，通常包含了许多计算核心和内存控制器等组件，这些组件可以同时处理多个计算任务，从而提高计算性能和效率。


## GPU的算力
GPU（图形处理器）的算力指的是其在数值计算方面的性能，通常用浮点运算速度（FLOPS）来衡量。FLOPS是指每秒浮点运算次数，它是衡量计算机处理速度的一种常用指标。
GPU的算力在很大程度上决定了其在图形渲染、科学计算、深度学习等计算密集型任务中的表现。随着GPU技术的不断发展，现代GPU的算力已经非常强大，可以进行大规模的并行计算，加速许多应用程序的执行速度。

## GPU的体系结构
1. 处理器核心：GPU通常包含数千个处理器核心，每个核心可以执行多个线程。这些核心被组织成一个或多个处理器阵列，以便并行处理任务。
2. 存储器：GPU包含多个存储器层次，包括全局存储器、共享存储器和寄存器文件。全局存储器用于存储GPU程序的数据，共享存储器用于同一线程块中的线程之间共享数据，寄存器文件则用于存储线程的本地变量。
3. 内存控制器：GPU包含多个内存控制器，用于管理GPU的存储器子系统。内存控制器协调GPU核心和存储器之间的数据传输，并决定何时将数据从存储器中读取或写入。
4. 编程模型：GPU通常使用并行计算模型，例如CUDA或OpenCL。这些模型允许程序员将任务分解为多个并行线程，并将这些线程映射到GPU核心上，以便高效地利用GPU的并行处理能力。

总体而言，GPU的体系结构旨在提供高效的并行计算能力，以加速各种计算密集型应用程序的运行。

## GPU 线程结构

![](/img/in-post/post-ai/hardware/gpu/gpu-thread-1.png)

* GPU 并行才是本质
* CPU 并发是本质

![](/img/in-post/post-ai/hardware/gpu/gpu-cpu-1.png)

## gpu的基本概念

### CUDA
CUDA（Compute Unified Device Architecture）
1. 2006 年 11 月，NVIDIA 推出了 CUDA，通用并行计算平台和编程模型，用于图形处理单元（GPU）上的通用计算。利用 NVIDIA GPU 中的并行计算引擎（CUDA Core）以比在 CPU 上更有效的方式解决许多复杂的计算问题。
![](/img/in-post/post-ai/hardware/gpu/cuda-architecture.png)
2. CUDA 并行编程模型作为一款通用接口，为熟悉 C 等标准编程语言的程序员保持较低的学习曲线。
![](/img/in-post/post-ai/hardware/gpu/cuda-programing-architecture.png)
3. CUDA 将 GPU 架构建模为多核系统，将 GPU 并行线程抽象为层次化线程结构（网格状的线程块）。
4. 这样可以指导开发者将问题划分为独立线程块并行解决的子问题，并进一步将每个子问题划分为可以由块内的线程并行协作解决的部分。
![](/img/in-post/post-ai/hardware/gpu/cuda-programing-architecture-2.png)
5. 通过允许线程 Thread 在解决每个子问题时，进行协作来保持语言表达能力，同时实现自动可扩展性。实际上，每个线程块 Thread Block 都可以在 GPU 的计算单元内以任何顺序进行调度，以便编译后的 CUDA 程序可以在任意数量的多处理器上执行。


### SP，流处理器 (Stream Processor)
* SP 被称作流处理器，在G80时英文也曾称（Scalar Processors）、（Scalar Streaming Processors）。这种称呼主要因为之前 SP 单元是被当作标量处理单元。Fermi 架构后，SP被改称为CUDA Core。所以对于现在的N卡架构来说，流处理器数量即CUDA Core数量。


### sm是指什么

1. SM 从 G80 提出的概念，中文称流多处理器，包含算术单元以及块和线程专用的其他资源（例如每个块共享内存和寄存器文件），组成了一套完整的线程运行系统。主要包括：
   1. CUDA Core：向量运行单元 （FP32-FPU、FP64-DPU、INT32-ALU）；
   2. Tensor Core：张量运算单元（FP16、BF16、INT8、INT4）；
   3. Special Function Units：特殊函数单元 SFU（超越函数和数学函数，e.g. 反平方根、正余弦等）；
   4. Multi level Cache：多级缓存（L0/L1 Instruction Cache、L1 Data Cache & Shared Memory）；
   5. Load/Store：访问存储单元LD/ST（负责数据处理）；
   6. Warp Scheduler：线程束调度器；
   7. Dispatch Unit：分配单元；
   8. Register File：寄存器堆；

在GPU中，SM（Streaming Multiprocessor）是一种物理硬件单元，它包含了一组CUDA核心、寄存器文件、共享内存和其他硬件资源，可以独立地执行指令。
SM是GPU的核心组成部分，也是GPU进行并行计算的基本单元。每个SM可以独立执行指令，包括算术运算、逻辑运算、数据移动、共享内存等，从而实现对大规模数据的高效并行处理。
不同的GPU型号和架构中，SM的具体实现和性能可能会有所不同。例如，NVIDIA的Turing架构中，每个SM中包含64个CUDA核心、4个Tensor核心和其他硬件资源，可以同时执行多个线程块。SM的数量和性能也会影响GPU的总体性能。

### Warp
1. Warp 也称线程束。逻辑上，所有Thread是并行，但是，从硬件的角度来说，并不是所有的 Thread能够在同一时刻执行，这里就需要Warp的引入。
2. Warp 是 SM 基本执行单元，一个 Warp 包含32个并行 Thread（warp_size = 32），这32个 Thread 执行于 SIMT（Single-Instruction Multiple-Thread）模式。也就是说所有 Thread 以锁步的方式执行同一条指令，但每个 Thread 会使用各自的 Data 执行指令分支。如果在 Warp 中没有32个 Thread 需要工作，那么 Warp 虽然还是作为一个整体运行，但这部分 Thread 是处于非激活状态的。
3. Thread是最小的逻辑单位，Warp是最小的硬件执行单位。

### CUDA Core
1. CUDA Core 在 Fermi 架构里提出，是最小的运算执行单元。
2. 在 Fermi 架构中，一个 SM 中包含了有 2 组各 16 个 CUDA Core，
3. 每个 CUDA Core 包含了一个整数运算单元 ALU (Integer Arithmetic Logic Unit) 和一个浮点运算单元 FPU (Floating Point Unit) 。
![](/img/in-post/post-ai/hardware/gpu/fermi_Streaming_Multiprocessor.png)

4. 到了 Volta 架构，CUDA Core 又和 Fermi架构时期发生了变化。从这里开始就没有以前的 CUDA Core 了，而是变成了单独的 FP32  FPU 和 INT32 ALU。
5. 因为 FP32:INT32 是 1:1，所以还是很方便把它们合并成原来的 CUDA Core 去称呼。这样做的好处是每个 SM 现在支持 FP32 和 INT32 的并发执行。

![](/img/in-post/post-ai/hardware/gpu/volta-streaming-multiprocessor.png)


## GPU的显存与sm的关系是什么

GPU的显存和SM之间存在紧密的关系，显存是存储GPU处理数据和指令所需的内存，而SM则是负责执行这些指令的计算单元。

在CUDA编程中，程序员需要将数据和指令分配到显存中，然后使用CUDA核函数来调用SM执行计算。SM可以从显存中读取数据、执行指令，并将结果写回显存。因此，显存的大小和速度都会对GPU的性能产生重要影响，影响SM执行指令的速度和效率。

具体来说，当SM需要读取显存中的数据时，如果该数据不在SM的本地缓存中，那么SM需要从显存中读取数据，这将产生一定的延迟。此外，如果显存的带宽不足，那么SM读写显存的速度将会受到限制，进而影响整个GPU的性能。

因此，为了获得最佳的GPU性能，需要根据具体的应用场景来确定显存和SM的配置，以及最优的显存带宽和延迟。

## CUDA core 与tensor core的关系

CUDA core是英伟达（NVIDIA）GPU中用于执行CUDA（Compute Unified Device Architecture）计算任务的处理器单元。CUDA core是一种基于SIMD（Single Instruction, Multiple Data）并行计算架构的处理器，可以执行相同指令的多个数据元素。在一个CUDA核心内部，有多个ALU（Arithmetic Logic Unit），这些ALU可以同时执行同一指令的多个线程，从而实现并行计算。

每个CUDA核心可以执行多个线程，这些线程被组织成线程块（block），线程块被组织成网格（grid）。在CUDA编程模型中，程序员可以利用这些线程块和网格来实现高度并行的计算，从而利用GPU的并行计算能力，提高计算效率。

不同的NVIDIA GPU型号具有不同数量的CUDA core，通常高端GPU具有更多的CUDA core，可以处理更多的并行计算任务。同时，CUDA core也是评估GPU计算性能的重要指标之一。

Tensor Core是英伟达（NVIDIA）GPU中的一种特殊处理器单元，用于执行矩阵乘法和深度学习中的张量操作。Tensor Core是一种硬件加速器，可以高效地执行矩阵计算，并且在一次操作中可以处理多个数据元素。

Tensor Core在实现深度学习中的矩阵计算时可以极大地提高计算速度，使得深度学习训练和推理的速度大大加快。具体来说，Tensor Core支持半精度浮点数（FP16）和混合精度浮点数（FP16+FP32）的矩阵运算，可以在一次操作中同时计算四个FP16或者两个FP16+FP32数据元素。

Tensor Core一般用于英伟达的深度学习加速库CUDA（Compute Unified Device Architecture）中，如cuDNN（CUDA Deep Neural Network library）等。Tensor Core的出现大大加速了深度学习模型的训练和推理速度，成为了当今深度学习加速的重要技术之一。

## GPU 显存速度

![](/img/in-post/post-ai/hardware/gpu/gpu-cache.png)


## 参考文件
1. > [Nvidia Cuda 编程]
   (https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#/) 

2. > [Nvidia GPU架构 ]
   (https://blog.csdn.net/asasasaababab/article/details/80447254) 