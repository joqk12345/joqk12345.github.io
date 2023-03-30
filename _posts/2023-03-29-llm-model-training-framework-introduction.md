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


## 参考文件

1.  > [ GPT-3 + RL 全流程训练开源整理]
   (https://zhuanlan.zhihu.com/p/608705255) 


   



