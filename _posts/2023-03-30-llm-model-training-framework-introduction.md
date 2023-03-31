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
5. 

## ChatGPT 完整训练包括三个流程
Supervised FineTune（SFT）：使用人工编写的期望模型如何输出的数据集，对GPT-3进行微调
Reward Model（RM）：使用人工标注的排序数据，训练奖励模型，预测人类更喜欢哪个输出
强化学习微调 SFT：使用奖励模型作为强化学习优化目标，微调SFT模型


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


## 参考文件

1.  > [ GPT-3 + RL 全流程训练开源整理]
   (https://zhuanlan.zhihu.com/p/608705255) 


   



