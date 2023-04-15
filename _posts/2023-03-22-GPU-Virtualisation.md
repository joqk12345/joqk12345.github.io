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

## gpu的sm是指什么

在GPU中，SM（Streaming Multiprocessor）是一种物理硬件单元，它包含了一组CUDA核心、寄存器文件、共享内存和其他硬件资源，可以独立地执行指令。

SM是GPU的核心组成部分，也是GPU进行并行计算的基本单元。每个SM可以独立执行指令，包括算术运算、逻辑运算、数据移动、共享内存等，从而实现对大规模数据的高效并行处理。

不同的GPU型号和架构中，SM的具体实现和性能可能会有所不同。例如，NVIDIA的Turing架构中，每个SM中包含64个CUDA核心、4个Tensor核心和其他硬件资源，可以同时执行多个线程块。SM的数量和性能也会影响GPU的总体性能。

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

test
## 参考文件
1. > [Nvidia Cuda 编程]
   (https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#/) 

2. > [Nvidia GPU架构 ]
   (https://blog.csdn.net/asasasaababab/article/details/80447254) 