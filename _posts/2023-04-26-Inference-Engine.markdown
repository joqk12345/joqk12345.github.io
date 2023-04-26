---
layout:     post
title:      "Inference Engine"
subtitle:   "Inference Engine"
date:       2023-04-26 22:24:00
author:     "Galaxies"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Inference
    - model
---


> 
> “Yeah It's on. ”
>  
>  

## 推理引擎架构

### 模型转换与优化
*  模型格式转换工具
*  计算图优化

1. Converter Challenge
   1. 转换模块挑战与目标
      * AI 模型本身包含众多算子，推理引擎需要用有限算子实现不同框架 AI 模型所需要的算子
      * 支持不同框架 Tensorflow、PyTorch、MindSpore、ONNX 等主流模型文件格式
        * AI 训练框架随版本变迁会有不同的导出格式
        * AI 训练框架随版本变迁有大量的算子新增与修改
      * 支持 CNN / RNN / GAN / Transformer 等主流网络结构
      * 支持多输入多输出，任意维度输入输出，支持动态输入，支持带控制流的模型

   2.  转换模块应对策略
       1.  拥有自己的算子定义和格式对接不通的AI框架的算子层
       2.  自定义计算图IR，对接不通的AI框架极其版本
       3.  有丰富的demo和Benchmark提供主流模型的性能和功能基准
       4.  支持可扩展性和AI特性，对不同任务、大量集成测试和验证

2. Optimizer Challenge 
   1. 优化模块挑战
      1. 结构冗余:深度学习网络模型结构中的无效计算节点、重复的计算子图、相同的结构模块，可 以在保留相同计算图语义情况下无损去除的冗余类型;
      2. 精度冗余:推理引擎数据单元是张量，一般为FP32浮点数，FP32表示的特征范围在某些场景存 在冗余，可压缩到 FP16/INT8 甚至更低;数据中可能存大量0或者重复数据。
      3. 算法冗余:算子或者Kernel层面的实现算法本身存在计算冗余，比如均值模糊的滑窗与拉普拉 斯的滑窗实现方式相同。
      4. 读写冗余:在一些计算场景重复读写内存，或者内存访问不连续导致不能充分利用硬件缓存， 产生多余的内存传输。
    2. 应对策略
       1. 计算图优化，算子融合、算子替换、常量折叠
       2. 模型压缩，低比特量化、剪枝、蒸馏等
       3. 统一算子/计算表达/提升Kernel泛化性
       4. 数据排布优化、内存分配的优化
3. 转换模块步骤
   1. Converter由Frontends和Graph Optimize构成。前者负责支持不同的AI 训练框架;后者通过算 子融合、算子替代、布局调整等方式优化计算图:
   ![](/img/in-post/post-ai/inference/convert_work_flow.png)
---



## 结论



—— Galaxies

## 参考文献
1. >[Emergent Abilities of Large Language Models](https://arxiv.org/pdf/2206.07682.pdf)
2. >[https://zhuanlan.zhihu.com/p/621438653](https://zhuanlan.zhihu.com/p/621438653)