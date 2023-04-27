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
> 传统的推理需要干的事情
> “Yeah It's on. ”
>  
>  


## 模型转换与优化
*  模型格式转换工具
*  计算图优化
### . Converter Challenge
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
   3. 转换模块步骤
   4. Converter由Frontends和Graph Optimize构成。前者负责支持不同的AI 训练框架;后者通过算 子融合、算子替代、布局调整等方式优化计算图:
   ![](/img/in-post/post-ai/inference/convert_work_flow.png)

   5. 模型转换技术的设计思路
      1. 直接转换
         1. 直接将网络模型从 AI 框架转换为适合目标框架使用的格式;
         2. 步骤
            1. 内容读取： 读取 AI框架生成的模型文件，并识别模型网络中的张量数据的类型/格式、算子的 类型和参数、计算图的结构和命名规范，以及它们之间的其他关联信息。
            2. 格式转换:将 step1 识别得到的模型结构、模型参数信息，直接代码层面翻译成推理引擎支 持的格式。当然，算子较为复杂时，可在 Converter 中封装对应的算子转换函数来实现对推 理引擎的算子转换。
            3. 模型保存:在推理引擎下保存模型，可得到推理引擎支持的模型文件，即对应的计算图的显 示表示。
      2. 规范式转换
         1. 设计一种开放式的文件规范，使得主流 AI 框架都能实现对该规范标准的支持，以 ONNX 为代表；
         2. ONNX是一种针对机器学习所设计的开放式文件格式，用于存储训练好的网络模型。它使得不同的 AI 框架 (如Pytorch, MindSpore) 可以采用相同格式存储模型数据并交互。
         3. ONNX 定义了一种可扩展的计算图模型、一系列内置的运算单元(OP)和标准数据类型。每一 个计算流图都定义为由节点组成的列表，并构建有向无环图。其中每一个节点都有一个或多个 输入与输出，每一个节点称之为一个 OP。
   6. 模型转换通用流程
      1. AI框架生成计算图(以静态图表示)，常用基于源码 AST 转换和基于 Trace 的方式;
      2. 对接主流通用算子，并重点处理计算图中的自定义算子;
      3. 目标格式转换，将模型转换到一种中间格式，即推理引擎的自定义 IR;
      4. 根据推理引擎的中间格式 IR，导出并保存模型文件，用于后续真正推理执行使用。
      ![](/img/in-post/post-ai/inference/convert_process.png)


###  Optimizer Challenge 
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
   3. 计算图优化
      1. 基于一系列预先写好的模板，减少转换模块生成的计算图中的冗余计算，比如 Convolution 与 Batch Normal / Scale 的合并，Dropout 去除等。图优化能在特定场景下，带来相 当大的计算收益，但相当依赖根据先验知识编写的模板，相比于模型本身的复杂度而言注定是 稀疏的，无法完全去除结构冗余。
   4. 图优化的方式
      ![](/img/in-post/post-ai/inference/compute_graph_optimaztion.png)
      1. Basic: 基础优化涵盖了所有保留计算图语义的修改，如:O1常量折叠、O2冗余节点消除 和O3有限数量的算子融合。
      2. Extended: 扩展优化仅在运行特定后端，如 CPU、CUDA、NPU 后端执行提供程序时适 用。其针对硬件进行特殊且复杂的 Kernel 融合策略和方法。
      ![](/img/in-post/post-ai/inference/flash_attention.png)
      3. Layout & Memory: 布局转换优化，主要是不同 AI 框架，在不同的硬件后端训练又在不 同的硬件后端执行，数据的存储和排布格式不同。
         1. Inplaceoperation:如果一块内存不再需要，且下一个操作是element-wise，可以原地覆盖内存
         2. Memorysharing:两个数据使用内存大小相同，且有一个数据参与计算后不再需要，后一个数据可以覆盖前一个数据
      ![](/img/in-post/post-ai/inference/memory_optimation.png)
      
      

### 模型序列化
   1. 模型序列化：模型序列化是模型部署的第一步，如何把训练好的模型存储起来，以供后续的模型预测使用，是模型部署的首先要考虑的问题。
   2. 模型反序列化：将硬盘当中的二进制数据反序列化的存储到内存中，得到网络模型对应的内存对象。
   3. 无论是序列化与反序列的目的是将数据、模型长久的保存。
   ![](/img/in-post/post-ai/inference/Serialization.png)
   4. 序列化分类：跨平台跨语言通用序列化方法，主要优四种格式：XML，JSON，Protobuffer 和 flatbuffer。而使用最广泛为 Protobuffer，Protobuffer为一种是二进制格式。
   5. PyTorch 模型序列化 I:
      1. PyTorch 内部格式只存储已训练模型的状态，主要是对网络模型的权重等信息加载
      2. PyTorch 内部格式类似于 Python 语言级通用序列化方法 pickle。（包括 weights、biases、Optimizer）
   6. PyTorch 模型序列化 II:
      1. ONNX：内部支持 torch.onnx.export
   7. 目标文件格式:protobuffer / flatbuffer
      1. Protobuffer 编码模式:计算机里一般常用的是二进制编码，如int类型由32位组成，每位代表数值2的n次方，n的范围是0-31。Protobuffer 采用 TLV 编码模式，即把一个信息按照 tag-length-value 的模式进行编码。tag 和 value 部分类似于字典的 key 和 value，length 表示 value 的长度，此外 Protobuffer 用 message 来作为描述对象的结构体。
      2. Protobuffer 解码模式： 根 message 由多个 TLV 形式的 field 组成，解析 message 的时候逐个去解析 field。由于 field 是 TLV 形式，因此可以知道每个 field 的长度，然后通过偏移上一个 field 长度找到下一个 field 的起始地址。其中 field 的 value 也可以是一个嵌套 message。对于 field 先解析 tag 得到 field_num 和 type。field_num 是属性 ID，type 帮助确定后面的 value 一种编码算法对数据进行解码。
      3. FlatBuffers：FlatBuffers 主要针对部署和对性能有要求的应用。以阿里MNN为代表的，相对于 Protocol Buffers，FlatBuffers 不需要解析，只通过序列化后的二进制buffer即可完成数据访问。FlatBuffers 的主要特点有：
         1. 数据访问不需要解析
         2. 内存高效、速度快
         3. 生成的代码量小
         4. 可扩展性强

### 自定义计算图
  1. 基于计算图的AI框架:基本组成：
     1. Tensor、Tensor形状: [2, 3, 4, 5]、 元素类型:int, float, string, etc.
     2. Operator：由最基本的代数算子组成、根据深度学习结构组成复杂算子、N个输入Tensor，M个输出Tensor
  2. Tensor 的表示
     1. 存储形式
     2. 内存排布形式
     3. Tensor的定义
   3. Operator的表示
      1. 算子列表
      2. 算子公共属性和特殊算子列表
      3. 算子的基础定义
   4. 计算图的表示
      1. 定义网络模型子图
      2. 定义网络模型
   5. 常用流程
      1. 构建计算图 IR:根据自身推理引擎的特殊 性和竞争力点，构建自定义的计算图
      2. 解析训练模型:通过解析 AI 框架导出的模 型文件，使用 Protobuffer / flatbuffer 提供的 API定义对接到自定义 IR 的对象
      3. 生成自定义计算图:通过使用 Protobuffer / flatbuffer 的API导出自定义计算图



## 结论



—— Galaxies

## 参考文献
