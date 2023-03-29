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

# inference-architecture

DL inference 指的是深度学习模型的推理阶段，即在模型训练完成后使用训练好的模型进行实际的预测或分类任务。在 DL inference 阶段，输入数据将通过已经训练好的深度学习模型，生成相应的输出结果。这个过程通常在生产环境中运行，因此要求具备高效、稳定和可靠的性能，以处理大量的实时数据。

![](/img/in-post/post-ai/inference/inference-architech.png)

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





## 参考文件

1.  > [ DeepLearningSystem]
   (https://github.com/chenzomi12/DeepLearningSystem) 



