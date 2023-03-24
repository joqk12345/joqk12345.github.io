---
layout:       post
title:        "ASR 建模单元"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - asr
    - ctc
    - token
    - unit
    - word
---

# ASR 建模
asr 常见模型目前已大类端到端和CTC两类为主介绍一下建模单元的选择。

## End-to-End 方案

建模单元本身直接出结果，会做一写后处理让subword 变成word

## CTC-WFST 方案

* 构图
TLG： 是CTC方案的时候需要构建TLG的fst的静态网络；
T代表： token 
L代表： Lexcion 词表
G代表： Gram  是word词表
L是链接Token 与 Words的映射；
在HMM时代是HCLG。
*  建模单元的选择

![](/img/in-post/post-ai/asr/ctc-wfst-architeck.png)

无LM，即依靠CTC prefix beam search生成N-best。
有LM，则依靠CTC WFST search生成N-best，WFST search为依靠传统解码图的传统解码方式。
有LM时的CTC WFST search是本文的核心。主要包含解码图的构建和解码器两大部分。


解码图的构建即将建模单元T、词典L、语言模型G各层次信息组合在一张解码图TLG中，其中：

1. T为端到端训练时的建模单元，一般的，中文建模单元为汉字，英文为英文字母char或者BPE，在英语体系中一般是subword建模，因为subword会少，并且包含_代表CTC中的blank，用于吸收垃圾帧；unit： 为声学建模单元，一般是指把token去掉_ ；
2. L为词典，该词典很简单，直接将一个单词拆分成其建模单元序列即可，如单词“我们”拆分成“我 们”两个字，单词 APPLE 拆分成“A P P L E”五个字母。可以看到，没有传统词典中音素的概念，也就无需人为干预设计发音序列。
3. G为语言模型，即将n-gram的语言模型转为WFST形式的表示。
解码器则和传统语音识别中解码器一致，使用标准的Viterbi beam search算法解码。word： 用于构建n-grammar语言模型他属于词表。

## TLG的构建流程

## 语言模型



## 参考文件

1.  > [ 常见的ASR模型架构 ]
   (https://docs.nvidia.com/deeplearning/nemo/user-guide/docs/en/main/asr/models.html) 
2. > [ ASR 语言模型 ]
   (https://docs.nvidia.com/deeplearning/nemo/user-guide/docs/en/main/asr/asr_language_modeling.html) 
3. > [ WeNet 支持语言模型]
   (https://zhuanlan.zhihu.com/p/371483056) 
4. > [ BPE ]
   ( https://arxiv.org/pdf/2205.00485.pdf) 






