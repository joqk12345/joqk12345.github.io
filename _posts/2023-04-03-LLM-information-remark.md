---
layout:       post
title:        "LLM 技术分析笔记"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - LLM Tech
    - GPT4
    - model aichitecture
    - foundation model
    - Model Architecture
    - Pretraining Objective
    - Zero-Shot Generalization
---

# LLM 引领下一代AI范式变革

对于整个ChatGPT引领的技术变革已经非常excting，并且对于新东西学习，发现研究者们对任何事情的起名字也非常高大上，学了不少（close domain、out of domain、open domain、prompt、instruct、RFHL、等等）非常有学习价值。

- 关于GPT-4 的了解可以看OpenAI的技术报告、以及微软的中文版报告
- LLM带来的范式转移以及未来技术发展趋势
    - 模型架构选择
        - 目前了解到的是，175b 参数量级是文本数据的上限
        - encoder style 是否还有竞争力
            - 大的bert模型跟gpt 1 还是有优势的
            - 但是范式已经改变，从supervistd到Unsupervised范式
            - bert cross domain ，因为他是双向的，主要针对确定性问题；
        - encode加decoder
            - 可控性强，双向理解能力，事实准确性理解会有帮助
            - encode加decoder 13b 计算效率变低，batch padding ，大规模并行容易训崩，比如layer norm
            - 想要与decoder-only 架构的对齐需要，算力对齐或者参数量对齐
        - decoder
            - 训练效率比较高，学习难度是最大的
            - 创造性、泛化性都是可以
    - 多模态的训练
        - 聊了一下方案，目前不确定性因素还是很多的
    - 大模型目前还是有很多很有意义的事情
        - 如何训练、如何微调、涌现能力的解释、如何让他变的更好等等，非常多有意思的问题
- ChatGPT复现的路径
    - 那篇历史发展以及讨论在继续学习一下

## 论文速读：哪种模型架构和预训练目标最适合零样本泛化？

* 动机
目前的大规模预训练语言模型已经具有一定的零样本泛化能力（即不需要显式地进行训练）就可以完成一定的任务。比如GPT3，T0，FLAN等等，然而不同的模型架构（Causal Decoder, Non-Causal Decoder和Encoder-Decoder）和不同的预训练目标对这些模型有着十分重大的影响。然而这些因素很少被系统的探究过，本文作者致力于系统地探究以上因素对预训练模型在零样本泛化上的性能的影响。
* 方案
![](/img/in-post/post-ai/model/model_architech.png)
如上图所示，作者探究了三种模型架构（Causal Decoder, Non-Causal Decoder和Encoder-Decoder），三种预训练目标 Language Modeling (GPT系列)，Prefix LM (UniLM等)和 MLM（BERT系列），两种适配方案（对Causal Decoder使用MLM进行适配，做法上就是把attention变成Non-Causal的，对Non-Causal Decoder做LM的适配），两种微调选择（不进行任何微调和在下游任务上进行多任务微调）最后在两个基准上进行评估。
* 结论

通过系统而公平的比较作者得到了如下结论/发现：
基于Causal Decoder这种模型架构，使用full language modeling这种预训练目标经过大规模无监督预训练之后可以达到很好的零样本泛化性能。（换句话说，就是类似GPT系列的工作，纯decoder的模型，经过大规模自监督预训练就可以达到很好的零样本性能）
基于Encoder-Decoder这种模型架构，使用MLM这种预训练目标，通过下游的多任务微调之后可以达到很好的零样本泛化性能。（T0可以佐证这一点）
模型可以从一种架构/预训练目标适配到另外一种吗？作者发现只有单独Decoder架构的模型可以做到，另外作者推荐使用Causal Decoder使用full language modeling预训练然后用non-causal MLM预训练目标进行适配，然后再去进行下游任务的多任务微调，可以取得不错的效果。


## 参考文件

1. >[ GPT-4 技术报告 ]
   (https://cdn.openai.com/papers/gpt-4.pdf) 
2. >[微软GPT4 评测报告-en/zh]
   (https://ask.qcloudimg.com/draft/8642415/na64oeidz2.pdf)
   (https://ask.qcloudimg.com/draft/8642415/7jbe0upcgg.pdf)
3. > [Awesome-ChatGPT]
   (https://github.com/dalinvip/Awesome-ChatGPT)
4. > [紧跟GPT风向]
   (https://szqxz4m7fs.feishu.cn/docx/UhuedqlJVo7rl9xOKfuchSQqn4g)
5. > [Why Can GPT Learn In-Context]
    (https://arxiv.org/abs/2212.10559)
6. > [UL2: Unifying Language Learning Paradigms]
    (https://arxiv.org/pdf/2205.05131.pdf)
7. > [What Language Model Architecture and Pretraining Objective Work Best for Zero-Shot Generalization?]
    (https://arxiv.org/abs/2204.05832)
