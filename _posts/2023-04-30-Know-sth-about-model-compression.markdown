---
layout:     post
title:      "Model Compression"
subtitle:   "关于模型压缩的介绍与讨论"
date:       2023-04-30 12:24:00
author:     "Galaxies"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - compression
    - model
    - quant
    - pruning
---


>
> 模型压缩的最终目的是得到更小性能更好的小模型，如何做呢，我们开始吧
> “Yeah It's on. ”
>  
>  

# 模型压缩

主要的优化目标：
1. 减少内存密集的访问量，剪枝
2. 提高获取模型参数的时间，量化
3. 加速模型推理的时间

## 低比特量化（Low bit quantization）

模型量化是一种将浮点计算转成低比特定点计算的技术，可以有效的降低模型计算强度、参数大小和内存消耗。尤其是在极低比特(<4bit)、二值网络(1bit)、甚至将梯度进行量化时，带来的精度挑战更大。这里一起去了解下低比特量化的计算公式和基本方法，深入量化的原理。

### 问题
1. 为什么模型量化技术能够对实际部署起到加速作用? 
2. 为什么需要对网络模型进行量化压缩?
   1. 加速
3. 为什么不直接训练低精度的模型?(大模型呢?)
4. 什么情况下不应该/应该使用模型量化?

### 概念
1. 基本概念
   
2. 量化原理
    1. 模型量化桥接了定点与浮点，建立了一种有效的数据映射关系，使得以较小的精度损失代价获 得了较好的收益
    2. 要弄懂模型量化的原理就是要弄懂这种数据映射关系，浮点与定点数据的转换。
   ```
   Q = R/S + Z
   R = (Q -Z ) * S
   ```  
   Q: 表示量化后的定点数据
   R: 表示输入浮点数
   S: 表示缩放因子的数值(Scale)
   Z: 表示零点（zore-point）的数值

3. 量化类型
   1. 线性量化可分为对称量化和非对称量化
4. 量化方法
   1. AQT(Quant Aware Training, QA T)：训练感知量化
   2. PTQ(Post Training Quantization Dynamic)
      训练后量化分为动态离线量化（Post Training Quantization Dynamic, PTQ Dynamic）和静态离线量化（Post Training Quantization Static, PTQ Static），不管是哪种量化方式，同样需要在端侧真正部署起来。
      1. PTQ Dynamic
         1. 定义： 仅将模型中特定算子的权重从FP32类型映射成 INT8/16 类型
         2. 应用场景：主要可以减小模型大小，对特定加载权重费时的模型可以起到一定加速效果；例如ASR的AED模型，做完权重的量化之后显存降低一半，可以使用半卡方案增大吞吐；
         3. 但是对于不同输入值，其缩放因子是动态计算，因此动态量化是几种量化方法中性能最差的
      2. PTQ Static
         1. 定义： 校正量化或者数据集量化
         2. 核心：使用少量无标签校准数据，核心是计算量化比例因子。因为使用静态量化后的模型进行预测，在此过程中量化模型的缩放因子会根据输入数据的分布进行调整。
         3. 目标：求取量化比例因子，主要通过对称量化、非对称量化方式来求，而找最大值或者阈值的方法又有MinMax、KLD、ADMM、EQ等方法
         4. 常用的校准法：KL散度校准法
            1. KL散度校准法也叫相对熵，其中p表示真实分布，q表示非真实分布或p的近似分布:
            2. 相对熵，用来衡量真实分布与非真实分布的差异大小。目的就是改变量化域，实则就是改变真 实的分布，并使得修改后得真实分布在量化后与量化前相对熵越小越好。
        1. KL散度校准法的核心流程
           1. 选取validation数据集中一部分具有代表的数据作为校准数据集 Calibration
           2. 对于校准数据进行FP32的推理，对于每一层
              1.  收集activation的分布直方图
              2.  使用不同的threshold来生成一定数量的量化好的分布
              3.  计算量化好的分布与FP32分布的KL divergence，并选取使KL最小的threshold作为saturation的阈值
            3. 通俗地理解，算法收集激活 Act直方图，并生成一组具有不同阈值的8位表示法，选择具有最少kl 散度的表示;此时的 kl 散度在参考分布(FP32激活)和量化分布之间(即8位量化激活)之间。
        2. 应用：TensorRT 就是在这个阶段进行的量化
 5. 量化推理部署
    1. 用IN T8进行inference时，由于数据是实时的，因此数据需要在线量化，量化的流程如图所示。 数据量化涉及Quantize，Dequantize和Requantize等3种操作。
    2. Quantize量化
       1. 将float32数据量化为int8。
    3. Dequantize反量化
      1. INT8相乘、加之后的结果用INT32格式存储，如果下一Operation需要float32格式数据作为输入，则通过Dequantize反量化操作将IN T32数据反量化为float32。
     4. Requantize重量化
        1. INT8乘加之后的结果用INT32格式存储，如果下一层需要INT8格式数据作为输入，则通过Requantize重量化操作将IN T32数据重量化为IN T8。
  6. GPTQ 量化
     1. 最近在整大模型量化相关的事情发现了这个[GPTQ for LLaMa]（https://github.com/qwopqwop200/GPTQ-for-LLaMa）
     2. GPTQ的核心原理来自于OBQ， 而OBQ的思路主要来自于OBS(Optimal Brain Surgeon)， 在OBS中，作者希望找到一种方法，假设我们要抹去一个权重记为 ,使得其对整体的误差增加最少，并且同时计算出一个补偿$\delta_q$应用于剩余的权重上，使得抹去的这个权重增加的误差被抵消。
     3. 关于这个方法的细节后面有机会单独介绍一下，这个只是记录一下大模型在量化方面的工作进展；
     4. 当前重点还是在llama的4bit量化方面；
     



## 模型剪枝（pruning）

1. 剪枝与量化区别
   1. 模型量化是指通过减少权重表示或激活所需的比特数来压缩模型。
   2. 模型剪枝研究模型权重中的冗余， 并尝试删除/修剪冗余和非关键的权重。

2. 基本概念
![](/img/in-post/post-ai/inference/compression/pruning.png)   
模型剪枝研究模型权重中的冗余， 并尝试删除/修剪冗余和非关键的权重。虽然模型剪枝的算法层出不穷，近年来也是越来越多的剪枝算法被工业界所真正应用，但究其分类主要分为Unstructured Pruning（非结构化剪枝）和 Structured Pruning（结构化剪枝）两种。

3. 谷歌研究pruning的一些观点
   1. 论文 To prune, or not to prune: exploring the efficacy of pruning for model compression
      1. 在内存占用相同情况下，大稀疏模型比小密集模型实现了更高的精度。
      2. 经过剪枝之后稀疏模型要优于，同体积非稀疏模型。
      3. 资源有限的情况下，剪枝是比较有效 的模型压缩策略。

4. 分类
   1. 结构化剪枝，非结构化剪枝
   2. 现在主要用的是结构化剪枝
      1. 优点：大部分算法在 channel 或者 layer 上进行剪枝，保留原始卷积结构，不需要专用硬件来实现
      2. 缺点：剪枝算法相对复杂

5. 模型剪枝流程
   1. 训练一个模型 -> 对模型进行剪枝 -> 对剪枝后模型进行微调
   2. 对应于  生成Beachmark  -> 模型稀疏化  --> 精度恢复

6. 常见的剪枝算法
   1. L1-norm 剪枝算法 etc

## 知识蒸馏（Knowledge Distillation）


1. 背景
* Knowledge Distillation(KD)最初是 Hinton 在 “Distilling the Knowledge in a Neural Network”提出，与 Label smoothing 动机类似，但是 KD 生成 soft label 方式通过教师网络得到。
* KD 可以视为将教师网络学到的知识压缩到学生网络中，另外一些工作 “Circumventing outlier of auto augment with knowledge distillation”则将 KD 视为数据增强方法的一种。
 
2. 概念

![](/img/in-post/post-ai/inference/compression/distillation.png)   

Student Model 学生模型模仿 Teacher Model 教师模型，二者相互竞争，直到学生模型可以 与教师模型持平甚至卓越的表现

3. 知识的方式
   1. ![](/img/in-post/post-ai/inference/compression/knowledge.png)   
   2. Response-Based Knowledge
      1. 死机硬背型
   3. Feature-Based Knowledge
      * 基于过程的：详细分解型
   4. Relation-Based Knowledge
      * 混合型
4. 蒸馏方法
   1. offline distillation, 2)online distillation，3)self-distillation 三种蒸馏方法可以看做是学习过程:
      1. Offline Distillation 指知识渊博教师向传授学生知识;
      2. Online Distillation 是指教师和学生共同学习;
      1. Self-Distillation 是指学生自己学习知识。

5. 基本概念 
   1. Hard-target and  Soft-target
      1. 传统的神经网络训练方法是定义一个损失函数，目标是使预测值尽可能接近于真实值(Hard- ta rget)，损失函数就是使神经网络的损失值和尽可能小。这种训练过程是对ground truth求极大 似然。在知识蒸馏中，是使用大模型的类别概率作为 Soft-target 的训练过程。
      ![](/img/in-post/post-ai/inference/compression/hard-target.png)   
   2. Softmax with Temperature
         Softmax function
         ```
         𝑞! = 𝑒𝑥𝑝(𝑧!)/ ∑" 𝑒𝑥𝑝(𝑧")
         ```
         Softmax with Temperature
         ```
         𝑞! = 𝑒𝑥𝑝(𝑧! /𝑇)/∑" 𝑒𝑥𝑝(𝑧"/𝑇)
         ```
      1. 其中 𝑞i 是每个类别输出的概率，𝑧i 是每个类别输出的 logits，T 是温度。温度 T=1 时，为标准 Softmax。T越高，softmax 的output probability distribution越趋平滑，其分布的熵越大，负标签 携带的信息会被相对地放大，模型训练将更加关注负标签。
      2. Logits 是指模型在没有经过激活函数（如 softmax）处理的输出结果。在机器学习中，模型通常将输入数据转化为一系列输出值，称为 logits。这些值可以表示模型在不同类别上的置信度或概率分布，但是它们通常需要进一步经过激活函数转化为对应的概率值。Logits 是模型在训练过程中的重要输出结果，可以用于计算损失函数并进行梯度下降优化。在训练结束后，我们通常使用模型的激活函数处理后的输出结果进行预测。
      3. 如何选择 T?
         * 当想从负标签中学到一些信息量的时候，温度T应调高一些; 
         * 当想减少负标签的干扰的时候，温度T应调低一些;

   3. 与传统训练流程的区别
      1. 其中KD的训练过程和传统的训练过程的对比:
         1. 传统training过程 Hard Targets: 对 ground truth 求极大似然 Softmax 值。
         2. KD的training过程 Soft Targets: 用 Teacher 模型的 class probabilities作为soft targets。
6. 例子：Hinton经典蒸馏算法(Distilling the Knowledge in a Neural Network)解读
   1. 使用类型
      1. 知识蒸馏使用offline distillation 的方式，采用经典的Teacher-Student 结构，其中 Teacher 是“知识” 的输出者，Student 是“知识”的接受者。知识蒸馏的过程分为2个阶段:
         1. 教师模型训练:训练 Teacher 模型, 简称为 Net-T，特点是模型相对复杂，精度较高。对 Teac her模型不作任何关于模型架构、参数量等方面限制。唯一的要求就是，对于输入 X , 其都能 输出 Y，其中 Y 经过 softmax 映射，输出值对应相应类别的概率值。
         2. 学生模型蒸馏:训练Student模型, 简称为 Net-S，它是参数量较小、模型结构相对简单的模型。 同样对于输入 X，其都能输出 Y，Y经过 softmax 映射后能输出与 Net-T 对应类别概率值。
   2. 算法架构
   ![](/img/in-post/post-ai/inference/compression/distilling-knowledge.png)   
   3. 主要思路
      1. 论文中，Hinton 将问题限定在分类问题下，或者属于分类的问题，共同点是模型最后有 soft max 层，其输出值对应类别的概率值。知识蒸馏时，由于已经有一个泛化能力较强的 Net-T， 在利用 Net-T 来蒸馏 Net-S 时，可以直接让 Net-S 去学习 Net-T 的泛化能力。
   4. 主要步骤
      1. 训练 Teacher Model;
      2. 利用高温 𝑇 high 产生 soft target;
      3. 使用 soft target, 𝑇 high 与 hard target, 𝑇 high，同时训练 Student Model;
      4. 设置温度 T = 1，Student Model用于线上推理;


—— Galaxies

## 参考文献
1. >[ 低比特量化](https://github.com/chenzomi12/DeepLearningSystem/blob/main/Inference/Slim/02.quant.pdf) 
2. >[ 神经网络量化综述](https://zhuanlan.zhihu.com/p/453992336) 
3. >[ GPTQ量化 ](https://zhuanlan.zhihu.com/p/616969812 )
4. >[ GPTQ: Accurate Post-Training Quantization for Generative Pre-trained Transformers ](https://arxiv.org/abs/2210.17323)