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

