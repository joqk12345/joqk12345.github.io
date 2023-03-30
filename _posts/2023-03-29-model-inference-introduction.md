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
Accelerate/Deepspeed-Inference/Deepspeed-ZeRO.

2. deepspeed Inference

3. Energon-AI
   
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

   



