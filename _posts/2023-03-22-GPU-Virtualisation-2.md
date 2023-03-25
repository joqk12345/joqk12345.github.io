---
layout:       post
title:        "GPU Virtualisation of  basic GPU serise"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - vcuda
    - rcuda
    - DeepLearning
    - gpu share
---

# GPU的资源共享

说明： unix-like os上计算容器场景的资源隔离能力，不包括win os，VM，视频，游戏相关方案。

GPU共享，是指在同一张GPU卡上同时运行多个任务。优势在于：（1）资源隔离，集群中可以运行更多任务，减少抢占。（2）资源利用率（GPU/显存/e.t.c.）提高；GPU共享后，总利用率接近运行任务利用率之和，减少了资源浪费。（3）可以增强公平性，因为多个任务可以同时开始享受资源；也可以单独保证某一个任务的QoS。（4）减少任务排队时间。（5）总任务结束时间下降；假设两个任务结束时间分别是x,y，通过GPU共享，两个任务全部结束的时间小于x+y。

想要实现GPU共享，需要完成的主要工作有：（1）资源隔离，是指共享组件有能力限制任务占据算力（线程/SM）及显存的比例，更进一步地，可以限制总线带宽。（2）并行模式，主要指时间片模式和MPS模式。[21]

## GPU share的原因

通常在推理集群做GPU Share的原因有

1. 成本：购买和维护专门的 GPU 集群或服务器需要大量的成本，而 GPU share 可以提供一个更便宜的解决方案。用户可以使用共享 GPU 资源来完成他们的工作，而无需自己购买昂贵的 GPU。

2. 节约资源：许多用户只需要在某些时间使用 GPU 来进行计算。这意味着 GPU 在大部分时间内会处于空闲状态，造成资源的浪费。GPU share 可以将这些空闲 GPU 资源共享给其他用户使用，做到的是多pod共享使用一张卡的算力，从而实现资源的最大化利用。

3. 灵活性：GPU share 可以提供更加灵活的 GPU 使用方案。用户可以根据自己的需要选择所需的 GPU 类型、数量和使用时间，而无需担心资源浪费或不足。

4. 技术难度：搭建和维护一个 GPU 集群或服务器需要大量的技术知识和经验。GPU share 可以让用户摆脱这些繁琐的技术细节，专注于他们自己的工作任务。

## 阿里的方案

GPU资源划分的维度指的就是GPU显存和Cuda Kernel线程的划分。通常在集群级别谈支持共享GPU，通常是两件事情：

1.调度
2.隔离，我们这里主要讨论的是调度，隔离的方案未来会基于Nvidia的MPS来实现。

阿里使用 cGPU（container GPU），提出的通过内核劫持来实现容器级GPU共享的方案，

技术原理：从应用级别控制显存的大小，利用类似gpu_options.per_process_gpu_memory_fraction控制应用的显存使用量。先简化到以显存为调度标尺，并且把显存使用的大小以参数的方式传递给容器内部。按显存和按卡调度的方式可以在集群内并存，但是同一个节点内是互斥的，不支持二者并存；要么是按卡数目，要么是按显存分配。

特点：能够让使用者通过API描述对于一个可共享资源的申请， 并能实现该种资源的调度、不支持该共享资源的隔离、不支持超卖

## 腾讯的vcuda方案





## 参考文件
1. > [盘点GPU工业界共享方案]
   (https://zhuanlan.zhihu.com/p/398369404/) 

2. > [阿里云异构计算产品]
   (https://www.alibabacloud.com/help/zh/elastic-gpu-service/latest/overview-of-heterogeneous-computing-services) 

3. > [阿里云gpushare-scheduler-extender]
   (https://github.com/AliyunContainerService/gpushare-scheduler-extender) 

4. > [阿里云gpushare-scheduler-extender-design]
   (https://github.com/AliyunContainerService/gpushare-scheduler-extender/blob/master/docs/designs/designs.md) 

5. > [What Is Shared GPU Memory]
   (https://www.cgdirector.com/what-is-shared-gpu-memory/) 

6. > [针对深度学习的GPU共享]
   (https://zhuanlan.zhihu.com/p/285994980/) 
