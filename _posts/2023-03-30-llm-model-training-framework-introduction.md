---
layout:       post
title:        "llm model traing framework introduction"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - training
    - llm
    - gpt3
    - RL
 
---

# ChatGPT 主流开源项目

1. Open-Assistant
2. ColossalAI
3. nebullvm
4. RaLM-rlhf-pytorch
5. DeepSpeed-Chat
6. lamini
7. Alpaca-LoRA:

## ChatGPT 完整训练包括三个流程
Supervised FineTune（SFT）：使用人工编写的期望模型如何输出的数据集，对GPT-3进行微调
Reward Model（RM）：使用人工标注的排序数据，训练奖励模型，预测人类更喜欢哪个输出
强化学习微调 SFT：使用奖励模型作为强化学习优化目标，微调SFT模型

## 关于LLM fine-tune技术
![](/img/in-post/post-ai/training/fine-tuning/llm-fine-tuning.png)

1. fine-tuning技术
   经典的fine-tuning方法包括将预训练模型与少量特定任务数据一起继续训练。在这个过程中，预训练模型的权重被更新，以更好地适应任务。所需的fine-tuning量取决于预训练语料库和任务特定语料库之间的相似性。如果两者相似，可能只需要少量的fine-tuning。如果两者不相似，则可能需要更多的fine-tuning。
   在NLP中，fine-tuning最著名的例子之一是由OpenAI开发的OpenAI GPT（生成式预训练变压器）模型。GPT模型在大量文本上进行了预训练，然后在各种任务上进行了微调，例如语言建模，问答和摘要。经过微调的模型在这些任务上取得了最先进的性能。
2. parameter-efficient fine-tuning技术
   通过仅训练一小组参数来解决传统微调技术需要大量资源的问题，这些参数可能是现有模型参数的子集或新添加的一组参数。
   1. fine-tuning技术称为蒸馏(distillation)：预训练模型生成“教师”预测结果，然后用于训练较小的“学生”模型。通过这样做，学生模型可以从较大模型的知识中学习，而无需存储所有参数。
   2. adapter training：适配器是添加到预训练模型中的小型神经网络，用于特定任务的微调。这些适配器只占原始模型大小的一小部分，这使得训练更快，内存需求更低。适配器可以针对多种任务进行训练，然后插入到预训练模型中以执行新任务。
3. prompt-tuning技术
   prompt-tuning和传统的fine-tuning的主要区别在于预训练模型被修改的程度。fine-tuning修改模型的权重，而提示调整只修改模型的输入。因此，prompt-tuning调整比精调的计算成本低，需要的资源和训练时间也更少。此外，prompt-tuning比精调更灵活，因为它允许创建特定任务的提示，可以适应各种任务。
   经典的prompt-tuning方式不涉及对底层模型的任何参数更新。相反，它侧重于精心制作可以指导预训练模型生成所需输出的输入提示或模板。这通常是一个手动的试错过程，从中选择最适合特定任务的提示。然而，随着前缀调整和P-Tuning等提示调整技术的出现，它们提供了更系统化和高效的方式来适应输入提示，从而提高大型预训练模型在特定任务上的性能。
   

## 关于训练框架训练模型的选择


先看一下众多的模型，感受一下：
···
BLOOM: A 176B-Parameter Open-Access Multilingual Language Model
Training language models to follow instructions with human feedback
Improving alignment of dialogue agents via targeted human judgements
LaMDA: Language Models for Dialog Applications
Scaling Language Models: Methods, Analysis & Insights from Training Gopher
PaLM: Scaling Language Modeling with Pathways
Language Models are Few-Shot Learners
Language Models are Unsupervised Multitask Learners
GLaM: Efficient Scaling of Language Models with Mixture-of-Experts
Using DeepSpeed and Megatron to Train Megatron-Turing NLG 530B, A Large-Scale Generative Language Model
On the Role of Bidirectionality in Language Model Pre-Training
WebGPT: Browser-assisted question-answering with human feedback
UL2: Unifying Language Learning Paradigms
OPT: Open Pre-trained Transformer Language Models
LLaMA: Open and Efficient Foundation Language Models
···
面对如此众多，令人众多的开源的模型，我们如何去选择预训练的框架和开源模型呢？我认为两点比较重要
1. 面对各家比拼，首先是数据部分，数据的清洗、多样性、去偏见等这个各家各取所长很肯没有放出来；
2. 选择的时候首要考虑是软件栈，一个好的软件栈能提升非常大的效率，最好包含预训练、SFT、RHLR、Inference、evaluation等各个步骤；
3. 如果挑好开源框架之后就是选择预训练模型了，首先要看框架支持的模型，然后在看这些模型的特点，以及自己的业务亲和度，就可以初步选出软件栈和预训练模型了。

## 常用训练框架一览
训练大型深度学习模型需要极大的内存，才能储存中间层的激活函数输出和权重等。一些模型只能在单个GPU上训练，训练时须将批大小（batch size）设置得极小；还有一些模型则太大，单个GPU放不下。这些问题会导致在某些情况下模型训练效率极低，甚至无法训练。训练大型深度学习模型主要有两大方法：数据并行、模型并行。
* 数据并行
单个GPU的内存可以完整容纳整个模型时，这是可实现数据并行的最简单的情况。但此时，模型训练可用的batch size就变得较小，增加了训练难度。解决办法是将不同的模型实例放在不同的GPU上运行，每个模型实例计算不同的数据批次。 
每个模型实例都有相同的参数初始化，但在前向传播中，每个模型实例会收到不同的数据批次。每个模型实例产生的梯度会被集结起来，用以计算梯度更新，然后进行模型参数更新，更新后的参数将被发送到每个模型示例中。
![](/img/in-post/post-ai/training/data-parallism.png)
* 模型并行
当单个GPU无法容纳整个模型时，模型并行就十分必要。模型并行要求将模型切分到多个GPU上进行训练。模型并行是训练大模型的有效办法，但它的缺点是算力的利用率太低。因为同一时间只有一个GPU正在运行，其余GPU都将闲置。 
![](/img/in-post/post-ai/training/model-parallism.png)

1. Megatron-LM
   1. Megatron是NVIDIA应用深度学习研究团队研发的大规模Transformer语言模型训练框架，支持模型并行（张量并行、序列并行与流水并行）与多节点预训练（multi-node pre-training），目前已支持BERT、GPT和T5模型。
   2. [Megatron-LM](https://github.com/NVIDIA/Megatron-LM)
2. DeepSpeed
   1. DeepSpeed是Microsoft推出的深度学习库，用于训练Megatron-Turing NLG 530B和BLOOM等大型模型，在训练、推理和压缩三方面都有所创新。
   2. DeepSpeed具有如下优点：
      1. 可进行十亿乃至万亿级参数的稀疏/密集模型的训练/推理
      2. 可实现超高系统吞吐量，可高效扩展至数千个GPU
      3. 可在资源有限的GPU系统上进行训练/推理
      4. 可实现前所未有的低延时与高推理吞吐量
      5. 可用低成本实现压缩，从而大幅降低推理延迟并缩减模型大小
      6. [DeepSpeed](https://github.com/microsoft/DeepSpeed)
3. FairScale
   1. FairScale是由Facebook Research开发的PyTorch扩展库，具备高性能，可用于大型训练。FairScale的愿景如下：
      1. 易用性，开发简单易懂的FairScale API，使用户方便上手。
      2. 模块化，实现模块化，使用户可将多个FairScale API无缝加入其训练循环。
      3. 性能，实现 FairScale API的高可扩展性和高效率。
    2. FairScale支持完全分片数据并行（FullyShardedDataParallel，FSDP），FSDP是扩展大型神经网络训练的推荐方法。
    3. [FairScale](https://github.com/facebookresearch/fairscale)
4. ParallelFormers
    1. Parallelformers是基于Megatron-LM的开源库。它与HuggingFace库高度融合，只用一行代码即可将HuggingFace库中的模型并行化。目前，Parallelformers只支持推理.
    2. [parallelformers](https://github.com/tunib-ai/parallelformers)
    3. 看代码自2022年7月后不更新
5. ColossalAI
   1. Colossal-AI提供用于分布式/并行训练的一套并行组件。Colossal-AI支持下列并行策略与优化措施：数据并行、流水并行、1D, 2D, 2.5D, 3D 张量并行、序列并行、零冗余优化器 (ZeRO)、异构内存管理（Heterogeneous Memory Management），用于推理系统Energon-AI。
   2. [ColossalAI](https://github.com/hpcaitech/ColossalAI)
6. Alpa
   1. Alpa是一个用于训练和服务大型神经网络的系统。其特点如下：
      1. 自动并行化：Alpa可自动将用户的单设备代码并行化，用于分布式集群，实现数据并行、算子并行和流水并行。
      2. 出色的性能：Alpa在使用分布式集群训练含十亿级参数的大模型时可实现线性扩展。
      3. 密切整合机器学习生态：Alpa由Jax、XLA和Ray等高性能且生产就绪的开源库提供支持。
   2. [alpa](https://github.com/alpa-projects/alpa) 
7. Hivemind
   1. Hivemind库可以让用户通过网络使用PyTorch进行去中心化深度学习训练。它起初的设计目的是让来自各地的高校、企业和自发参与人员能够通过数百台不同电脑训练同一大型模型。
    2. Hivemind的主要特点包括： 
       1. 可实现无主节点的分布式训练：其底层的分布式哈希表可在去中心化网络中连接所有计算机。
       2. 反向传播容错：即使某些节点无反应或反应时间较长，也不会导致前向传播和反向传播失败。
       3. 去中心化参数平均化: 来自多个worker的参数更新可以进行迭代累计，而无须在整个网络中同步。
       4. 可训练任意大小的神经网络：部分神经网络层可通过去中心化混合专家系统（Decentralized Mixture-of-Experts）分布在多台计算机中。
    3. [hivemind](https://github.com/learning-at-home/hivemind)
8. OneFlow
   1. OneFlow是一个高效、易用、可扩展的深度学习框架，可实现以下功能：
      1. 兼容PyTorch的API对模型进行编程
      2. 运用全局视角（Global View）API将模型扩展至n维并行执行或分布式执行
      3. 用静态图编译器（Static Graph Compiler）进行模型加速/部署
    2. [oneflow](https://github.com/Oneflow-Inc/oneflow) 
    3. [paper](https://arxiv.org/abs/2110.15032)
9.  Mesh-Tensorflow
   1.  Mesh TensorFlow (mtf)是一种专用于分布式深度学习的语言，可指定多种分布式张量计算。其名称中的Mesh意为“网”，表示多个处理器和计算设备互相连接形成的网络。
   2.  [mesh](https://github.com/tensorflow/mesh) 

10. DeepSpeed-Chat
    1.  出现的背景是： ChatGPT及类似模型引发了人工智能（AI）领域的一场风潮，ChatGPT类模型具有惊人的泛用性，能够执行归纳、编程、翻译等任务，其结果与人类专家相当甚至更优。在开源领域出现各种方案尝试去复现ChatGPT，比如（例如 ChatLLaMa、Alpaca、Vicuna、Databricks-Dolly等），尽管开源社区付出了巨大的努力，前仍缺乏一个支持端到端的基于人工反馈机制的强化学习（RLHF）的规模化系统，在现有的方案在规模、效率上都显得十分不足，成本巨大。
    2. DeepSpeed-Chat 特点
       1.  DeepSpeed-RLHF Pipeline 有自己的强化学习pipeline：简化 ChatGPT 类型模型的训练和强化推理体验，只需一个脚本即可实现多个训练步骤，包括使用 Huggingface 预训练的模型、使用 DeepSpeed-RLHF 系统运行 InstructGPT 训练的所有三个步 骤、甚至生成你自己的类ChatGPT模型。此外，我们还提供了一个易于使用的推理API，用于用户在模型训练后测试对话式交互。
       2.  DeepSpeed-RLHF 模块：DeepSpeed-RLHF 复刻了 InstructGPT 论文中的训练模式，并确保包括a) 监督微调（SFT），b) 奖励模型微调和 c) 基于人类反馈的强化学习（RLHF）在内的三个步骤与其一一对应。此外，我们还提供了数据抽象和混合功能，以支持用户使用多个不同来源的数据源进行训练。
       3.  DeepSpeed-RLHF System: 一将 DeepSpeed 的训练（training engine）和推理能力（inference engine) 整合到一个统一的混合引擎（DeepSpeed Hybrid Engine or DeepSpeed-HE）中用于 RLHF 训练。DeepSpeed-HE 能够在 RLHF 中无缝地在推理和训练模式之间切换，使其能够利用来自 DeepSpeed-Inference 的各种优化，如张量并行计算和高性能CUDA算子进行语言生成，同时对训练部分还能从 ZeRO- 和 LoRA-based 内存优化策略中受益。DeepSpeed-HE 还能够自动在 RLHF 的不同阶段进行智能的内存管理和数据缓存。
    3. Efficiency and Affordability: DeepSpeed Hybrid Engine 比现有系统（OPT模型有15x提升）；

11. lamini
    1.  20230429新出现的lamini，



## 参考文件

1.  > [ GPT-3 + RL 全流程训练开源整理]
   (https://zhuanlan.zhihu.com/p/608705255) 
2. > [训练大模型的九大深度学习库](https://blog.csdn.net/OneFlow_Official/article/details/127385341)
3. >[DeepSpeed Chat](https://github.com/microsoft/DeepSpeed/tree/master/blogs/deepspeed-chat)
4. >[DeepSpeed Chat-zh](https://github.com/microsoft/DeepSpeed/tree/master/blogs/deepspeed-chat/chinese#)
5. >[大模型微调技术](https://mp.weixin.qq.com/s/Ad-8OVCKxuVLH0e9__PHMA)
6. >[高效精调的论文综述](https://arxiv.org/abs/2303.15647)
7. >[P-Tuning和Prompt相关《GPT Understands, Too》](https://www.zhihu.com/tardis/zm/art/581378268?source_id=1003)
8. >[lamini](https://lamini.ai/blog/introducing-lamini)
9. >[Alpaca-LoRA](https://mp.weixin.qq.com/s/cTodp4lVeW6YP9NxoKhCcA)
10. >[LoRA: Low-Rank Adaptation of Large Language Models](https://finisky.github.io/lora/)






   



