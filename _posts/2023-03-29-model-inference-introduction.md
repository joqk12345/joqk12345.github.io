---
layout:       post
title:        "model-inference-introduction"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - inference
    - inference-architure
    - model-convertion
    - inference-optimization
    - distribute-inference
    - llm-inference
---

# DL Life cycle
AI目前的发展阶段已经经历了Arificial Intelligence、Machine Learning、Deep Learning、Foundation Models，这个时代，如今在事实上已经迈入大模型时代，确实发展比较快，仅此为了整理AI 相关的不变的理论范式、概念。

![](/img/in-post/post-ai/inference/AI_Develop_Stage.png)

* 训练：训练过程通过设计合适AI模型结构以及损失函数和优化算法，将数据集以mini-batch 反复进行前向计算并计算损失 ，反向计算梯度利用优化函数来更新模型，使得损失函数最小。训练过程最重要是梯度计算和反向传播。

![](/img/in-post/post-ai/inference/training.png)

* 推理：推理在训练好的模型结构和参数基础上 ，一次前向传播得到模型输出过程。相 对于训练，推理不涉及梯度和损失优化 。最终目标是将训练好的模型部署生产 环境中。

![](/img/in-post/post-ai/inference/inference-process.png)

* 训练任务:数据中心中更像是传统的批处理任务，需要执行数小时，数天才能完成，其一般配 置较大的批尺寸追求较大的吞吐，将模型训练达到指定的准确度或错误率。

* 推理任务:执行 7 X 24 的服务，其常常受到响应延迟的约束，配置的批尺寸更小，模型已经稳 定一般不再被训练。





## inference-architecture

DL inference 指的是深度学习模型的推理阶段，即在模型训练完成后使用训练好的模型进行实际的预测或分类任务。在 DL inference 阶段，输入数据将通过已经训练好的深度学习模型，生成相应的输出结果。这个过程通常在生产环境中运行，因此要求具备高效、稳定和可靠的性能，以处理大量的实时数据。

### 推理系统架构

![](/img/in-post/post-ai/inference/Inference-system.png)

推理系统关注的问题
  * 如何实现低延时高吞吐
  * 如何分配调度请求
  * 如何管理AI生命周期
  * 如何管理模型

### 推理引擎架构
  
![](/img/in-post/post-ai/inference/inference-architech.png)

推理引擎关注的问题
* 如何保持精度减少模型尺寸
* 如何对不同AI框架结果转换
* 如何保持精度下降减少模型尺寸
* 如何加快调度与执行
* 如何提高算子性能
* 如何利用边缘设备算力


* 模型格式转换
  * 用于离线部分
  * Frontends：模型格式转换,支持不同的AI 训练框架
  * Graph Optimize：计算图优化，通过算子融合、算子替代、布局调整等方式优化计算图
  

![](/img/in-post/post-ai/inference/model-convert(offline).png)

* 推理引擎的runtime
  * 在线推理使用
  * PyTorch
    * CPU多线程推理
    * Script推理
  * ONNXRuntime
    * 跨平台的模型推理框架，能够将 ONNX 模型部署到不同的硬件设备上进行推理。
  * OpenVINO
    * 英特尔针对自家硬件平台开发的一套深度学习工具库，包含模型推理、模型优化等一系列与深度学习模型部署相关的功能
  * TensorRT
    *  NVIDIA GPU 的高性能推理框架，通过提供一系列深度学习推理优化器和高效的 Runtime，实现了深度学习推理应用的低延迟和高吞吐。
  * FasterTransformer
    * CUDA、cuBLAS 的 Transformer Encoder 前向计算 C++ 实现，其在多种 BERT 的应用场景中表现出优越性能。在后续版本迭代中，FT 加入了 Decoder 的实现，从而进一步应用于翻译等更加复杂的文本任务。在 NVIDIA 开源项目 FT 之上，Effective Transformer（EFF-FT）项目加入了许多高级优化，在最新的 FT 版本中，相关功能已合并到 FT 代码仓库中。

## inference-brain-mind

![](/img/in-post/post-ai/inference/inference-brain-mind.png)

## model inference 

## llm inference

llm 时代给给AI计算模式带来新的思考
1. 芯片间互连技术，提供 X00GB/s 带宽
由于模型大，目前30B Llama 模型大概占用显存32G，已经超过传统的V100 架构的显存，模型并行以及数据高速互联技术的需求会越来越多。
2. 专用高速 Transformer 引擎
   * 大模型以 Transformer为基础结构进行堆叠，高速的 T ransformer计算
   * 更低比特 Transformer模块，并支持MoE构建万亿大模型

### 业内目前常用的推理系统

1. transformers-bloom-inference
hugging face 出品，支持不同的backend.
HF Accelerate/Deepspeed-Inference/Deepspeed-ZeRO.
  * BLOOM server deployment
    1.  以一般的服务启动。
    2.  服务目前支持的是同步，用户请求需要进入请求队列等待直到处理完成。

1. HF Accelerate
   1. 然后，它用钩子确保模型正常运行，在正确的设备上传输输入和输出，并确保在前向传递之前将卸载在CPU（甚至磁盘）上的模型权重加载到GPU上，然后在前向传递完成后再次卸载。在有足够空间容纳整个模型的多个GPU的情况下，它将控制权从一个GPU切换到下一个GPU，直到所有层都运行完毕。在任何时候都只有一个GPU在工作，这听起来效率很低，但它确实产生了体面的吞吐量，尽管GPU在空转。它也非常灵活，因为同样的代码可以在任何特定的设置上运行。加速器将首先使用所有可用的GPU，然后在CPU上卸载，直到RAM满了，最后在磁盘上卸载。卸载到CPU或磁盘会使事情变得更慢。作为一个例子，用户报告说，在不改变代码的情况下，仅在2个A100上运行BLOOM，每个令牌的吞吐量为15秒，而在8x80个A100上则为10毫秒。
   2. 总的来说使用了PP并行，并且使用了CPU的RAM、Disk以及一些异步的方式来达到节省VRAM的一个目的。
2. DeepSpeed-Inference
   1. DeepSpeed-Inference uses Tensor-Parallelism and efficient fused CUDA kernels to deliver a super-fast <1msec per token inference on a large batch size of 128.
3. Deepspeed ZeRO-Inference
   1. Deepspeed ZeRO使用了一种神奇的分片方法，它可以将几乎所有的模型，在几个或几百个GPU上进行扩展，并对其进行训练或推理。
   2. 脚本当前在所有 GPU 上运行相同的输入，但您可以在每个 GPU 上运行不同的流，并获得 n_gpu 倍的吞吐量。但是用 Deepspeed-Inference做不到到这一点，使用ZeRO，用户可以同时生成多个独特的数据流--因此，整体性能应该是以秒/令牌为单位的吞吐量除以参与的GPU数量--因此，根据使用8个或16个GPU，速度是8倍到16倍!
   3. sharding技术
      1. ZeRO 是deepspeed 提出的将各种占用显存的变量分拆到不同的device存储的方法，可以减少单个 GPU 上的内存压力，从而允许更大的模型被训练/推理。
      2. 在传统的分布式训练中，每个 GPU 上都会存储完整的模型参数副本，这导致了内存开销和网络通信的瓶颈。显存占用包括模型参数（fp16, fp32两份）, 优化器参数（比如Adam1, 2）, 梯度，中间计算结果(激活值)，每个device都保存一份参数和状态量副本；
      3. 而在 ZeRO 中，通过使用 sharding 技术，每个 GPU 上只需要存储部分模型参数，这就大大减轻了内存压力和网络通信负担。其中分为3级zero。
         1. ZeRO1：将Adam1,2分拆到不同device；
         2. ZeRO2：ZeRO1 + 拆分梯度；
         3. ZeRO3：ZeRO2 + 拆分模型参数；
      4. ZeRO 还利用了一些高效的算法来处理分片间的通信和同步，这使得多个 GPU 上的分片可以高效地协同工作，实现高效的模型训练。
      5. 类似的技术比较新版本的 pytorch 也已经集成了，来源于 fairscale 中的 FSDP；

4. 性能差异性的原因分析
   1. 吞吐量(throughput)的提升使用的Tensor Parallelism (TP)而不是Accelerate使用的Pipeline Parallelism (PP)。因为Accelerate就是非常传统的做法不能最大化GPU的使用率。所有的计算是基于GPU串行执行，例如首先是 GPU 0, 然后是在GPU 1等等. 指到执行到GPU 8,意味着每次执行有7个GPU是处于等待状态。另一方面，DeepSpeed-Inference使用TP，这意味着它将向所有GPU发送张量，在每个GPU上计算部分生成，然后所有GPU相互沟通结果，然后进入下一层。这意味着所有的GPU都是同时活动的，但它们需要的沟通要多得多。
   2. DeepSpeed-Inference还使用了定制的CUDA内核，以避免分配过多的内存和在GPU之间进行张量复制。这样做的效果是减少了内存需求和内核启动次数，从而提高了吞吐量，并允许更大的批处理量，从而提高整体吞吐量。

5. 但是看Benchmarks测试，accelerate会好于ds-inference，感觉很奇怪？？

6. Energon-AI
   
hpcaitech： HPC-AI Tech。 We are a global team to help you train and deploy your AI models。

####  解码策略

Accelerate/Deepspeed-Inference/Deepspeed-ZeRO.

#### 加载不同的模型size 与模型量化
1. 模型size
2. 模型量化：fp16、int8、int4
3. 模型加速： onnx、tensorRT



## 参考文件

1.  > [ DeepLearningSystem]
   (https://github.com/chenzomi12/DeepLearningSystem) 
2.  > [ transformers-bloom-inference ]
   (https://github.com/huggingface/transformers-bloom-inference) 
3. > [bloom](https://huggingface.co/bigscience/bloom)
4. > [ Incredibly Fast BLOOM Inference with DeepSpeed and Accelerate ](https://huggingface.co/blog/bloom-inference-pytorch-scripts)
5. > [  How to generate text: using different decoding methods for language generation with Transformers ](https://huggingface.co/blog/how-to-generate)
6.  > [ parallelism ](https://huggingface.co/transformers/v4.11.3/parallelism.html)

