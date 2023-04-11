---
layout:       post
title:        "Parallelism Training introduction "
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - data engineering
    - evaluation
    - model-architure
    - AI System Architecture
    - 3D
    - DD
    - DP
    - TP
    - ZeRo
---

# Deepspeed

Deepspeed是一个容易使用的深度学习优化软件套件，他为深度学习训练和推理提供前所未有的规模和速度。
* 训练/推理具有数十亿或数万亿参数的密集或稀疏模型
* 实现出色的系统吞吐量并有效地扩展到数千个GPU
* 实现前所未有的低延迟和高推理吞吐量
* 实现极致压缩，以低成本实现无与伦比的推理延迟和模型尺寸缩减

总的来说就是对于深度学习的模型训练和推理，具备极致的速度和规模效应，并且专注于训练、推理、与模型压缩三部分。

成果：
DeepSpeed trained the world's most powerful language models (MT-530B, BLOOM);
deepspeed是从零开始训练的第一步，是大模型训练的启动步骤。

## 概念澄清
* DeepSpeed 是一个深度学习优化库，让分布式训练变得简单、高效且有效。
* Megatron-LM 是由 NVIDIA 的应用深度学习研究团队开发的大型、强大的 transformer 模型框架。
* Megatron-DeepSpeed:DeepSpeed 团队结合了DeepSpeed、Megatron-LM 两种主要的技术，将DeepSpeed 库中的 ZeRO 分片和流水线并行 (Pipeline Parallelism) 与 Megatron-LM 中的张量并行 (Tensor Parallelism) 相结合，开发了一种基于 3D 并行的方案。HuggingFace的BigScience团队的176B BLOOM是由他训练的。其中组件结合的更多信息参见下图。
![](/img/in-post/post-ai/training/megatron-deepspeed.png)

# Parallelism
![](/img/in-post/post-ai/training/Parallelism.png)

## Data Parallelism，DP & DistributedDataParallel,DDP

数据并行 (Data Parallelism，DP) - 相同的设置和模型被复制多份，每份每次都被馈送不同的一份数据。处理是并行完成的，所有份在每个训练步结束时同步。属于Pytorch内置的能力。
* PyTorch 的 DistributedDataParallel(DDP)
在该方法中，模型被完全复制到每个 GPU，然后在每次迭代后所有模型相互同步各自的状态。这种方法可以通过投入更多 GPU 资源的方式加快训练速度，解决问题。但它有个限制，即只有当模型能够放进单个 GPU 时才有效。

## Tensor Parallelism，TP
1. 每个张量都被分成多个块，因此张量的每个分片都位于其指定的 GPU 上，而不是让整个张量驻留在单个 GPU 上。在处理过程中，每个分片在不同的 GPU 上分别并行处理，结果在步骤结束时同步。这就是所谓的水平并行，因为是做的水平拆分。
2. 在张量并行 (TP) 中，每个 GPU 仅处理张量的一部分，并且仅当某些算子需要完整的张量时才触发聚合操作
   *  Transformer 类模型的主要模块为: 一个全连接层 nn.Linear，后面跟一个非线性激活层 GeLU可以将其点积部分写为 Y = GeLU (XA)，其中 X 和  Y 是输入和输出向量， A 是权重矩阵，如果以矩阵形式表示的话，很容易看出矩阵乘法可以如何在多个 GPU 之间拆分
   *  如果将权重矩阵 A 按列拆分到 N 个 GPU 上，然后并行执行矩阵乘法 XA_1 到  XA_n，最终将得到 N 个输出向量 Y_1、Y_2、…… 、 Y_n ，它们可以独立输入 GeLU:
   *  因为 Y 矩阵是按列拆分的，因此随后可以选择按行拆分方案，这样它就可以直接获取前面层的 GeLU 的输出，而无需任何额外的通信
   *  使用该原理，可以更新任意深度的 MLP，只需在每个 拆列 - 拆行 序列之后同步 GPU
   ![](/img/in-post/post-ai/training/tp-1.png)
### 使用建议
1. TP 需要设备间有非常快速的信息通讯，除非有一个非常快的网络，否则不建议跨多个节点进行 TP。
2. 该组件由 Megatron-LM 实现。Megatron-LM 最近扩展了张量并行能力，新增了序列并行的能力，用于难以使用前述切分算法的算子，如 LayerNorm，
   1. Reducing Activation Recomputation in Large Transformer Models 论文提供了此技术的详细信息。序列并行是在训练 BLOOM 之后开发的，所以 BLOOM 训练时并未采用此技术
   2. Reducing Activation Recomputation in Large 
![](/img/in-post/post-ai/training/tp-2.png)
![](/img/in-post/post-ai/training/tp-3.png)


## Pipeline Parallelism，PP
模型在多个 GPU 上垂直 (即按层) 拆分，因此只有一个或多个模型层放置在单个 GPU 上。每个 GPU 并行处理流水线的不同阶段，并处理 batch 的一部分数据。


## Zero Redundancy Optimizer，ZeRO(data parallel)
![](/img/in-post/post-ai/training/zero-dp.png)
零冗余优化器也执行与 TP 相类似的张量分片，但整个张量会及时重建以进行前向或反向计算，因此不需要修改模型。它还支持各种卸载技术以补偿有限的 GPU 内存。
一些使用建议：
1. 通常ZeRO是一个独立的功能，不需要 PP 或 TP。但也可以与 PP、TP 结合使用。
2. 当 ZeRO-DP 与 PP (以及 TP) 结合时，它通常只启用 ZeRO 阶段 1，它只对优化器状态进行分片。ZeRO 阶段 2 还会对梯度进行分片，阶段 3 也对模型权重进行分片。
3. 虽然理论上可以将 ZeRO 阶段 2 与 流水线并行 一起使用，但它会对性能产生不良影响。每个 micro batch 都需要一个额外的 reduce-scatter 通信来在分片之前聚合梯度，这会增加潜在的显着通信开销。根据流水线并行的性质，我们会使用小的 micro batch ，并把重点放在算术强度 (micro batch size) 与最小化流水线气泡 (micro batch 的数量) 两者间折衷。因此，增加的通信开销会损害流水线并行。
4. 此外，由于 PP，层数已经比正常情况下少，因此并不会节省很多内存。PP 已经将梯度大小减少了 1/PP，因此在此基础之上的梯度分片和纯 DP 相比节省不了多少内存。
5. ZeRO 阶段 3 也可用于训练这种规模的模型，但是，它需要的通信量比 DeepSpeed 3D 并行更多。一年前，在对环境进行仔细评估后，发现 Megatron-DeepSpeed 3D 并行性表现最佳。此后，ZeRO 阶段 3 的性能有了显着提高，如果今天要对其进行重新评估，也许会选择阶段 3。



## 3D并行

1. DP+PP
![](/img/in-post/post-ai/training/3d-traing.png)

2. DP+PP+TP
![](/img/in-post/post-ai/training/3D-paralism.png)
![](/img/in-post/post-ai/training/3D-paralism-2.png)

由于每个维度至少需要 2 个 GPU，因此在这里你至少需要 8 个 GPU 才能实现完整的 3D 并行。




## 参考文件

1.  > [ DeepSpeed]
   (https://github.com/microsoft/DeepSpeed) 

2.  > [ Megatron-DeepSpeed]
   (https://github.com/bigscience-workshop/Megatron-DeepSpeed) 

3. > [ Megatron-DeepSpeed]
   (https://github.com/microsoft/Megatron-DeepSpeed) 

4.  > [ DeepSpeed]
   (https://www.deepspeed.ai/training/) 

5. > [ZeRO: Memory Optimizations Toward Training Trillion
Parameter Models]
   (https://arxiv.org/pdf/1910.02054.pdf)

6. > [Megatron-LM: Training Multi-Billion Parameter Language Models Using Model Parallelism]
   (https://arxiv.org/pdf/1909.08053.pdf)

7. >[ parallelism ](https://huggingface.co/transformers/v4.11.3/parallelism.html)

8. >[PyTorch DistributedDataParallel](https://pytorch.org/docs/master/generated/torch.nn.parallel.DistributedDataParallel.html
)
9. >[分布式并行](https://chhzh123.github.io/blogs/2019-05-11-dl-parallel/)
10. >[3D并行](https://www.deepspeed.ai/tutorials/pipeline/)
11. >[DeepSpeed: Extreme-scale model training for everyone](https://www.microsoft.com/en-us/research/blog/deepspeed-extreme-scale-model-training-for-everyone/)
12. >[pipedream](https://www.microsoft.com/en-us/research/uploads/prod/2019/08/pipedream.pdf)
13. >[ Reducing Activation Recomputation in Large Transformer Models ](https://arxiv.org/abs/2205.05198
)