---
layout:     post
title:      "llama.cpp decoder 策略"
subtitle:   "一种经过微调之后且加速的识别模型"
date:       2023-05-31 16:30:00
author:     "Galaxies"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - llama.cpp
    - transformer
    - BPE
    - Open Domain
    - decoder-only
---

> llama.cpp是基于Facebook的llama模型衍生出来的CPU为主的低成本解码方案，主打量化、CPU Run，这次我们聚焦于llama.cpp的解码方案。
> 同时抛出一些问题，比如在open-domain领域解码方案为什么不用beamsearch
> 
> “Yeah It's on. ”
>  
>  

## Transformer架构
基于Transformer的模型架构有两种，目前社区针对Transformer的推理运行时方案层出不穷，但是主要的模型结构是两种就是Encoder+Decoer和Decoder Only架构，针对语音本身的的需要Encoder+Decoder架构的以及NLP这种文本生成的以Decoder Only为主。
两种架构衍生出了Cross Attention 和 Self Attention的模式。
![](/img/in-post/post-ai/inference/fast-whisper/decoder/model-architure.png)   


### llama.cpp

Llama.cpp 是Llama 的一个C++的runtime实现，及Decoder Only的Transformer架构。
针对处理本身的概念：
1. BPE：BPE（Byte Pair Encoding）是一种常用的无损数据压缩算法，主要用于NLP或者语言模型当中，即将常见的字符或字符组合替换为较短的编码，从而实现数据压缩或词汇表示的有效性。在自然语言处理任务中，BPE编码常用于生成子词切分的词汇表。通过BPE编码，我们可以动态地学习词汇表，将常见的词汇合并为子词，并将未登录词拆分为常见的字符组合。这样可以在一定程度上解决词汇鸿沟（out-of-vocabulary）和未登录词（out-of-vocabulary words）问题。
   1. BPE编码的过程如下： 
      1. 初始化：将每个字符视为一个初始的符号。 
      2. 统计频次：对训练数据进行遍历，并计算每个字符或字符组合的出现频次。
      3. 合并最频繁的字符组合：在每次迭代中，选择最频繁出现的字符组合（由连续字符构成），将其合并为一个新的字符，并更新频次统计。
      4. 重复步骤3：不断重复步骤3，直到达到预设的合并次数或达到某个停止条件（例如词汇表大小）为止。
   2. 通过这个过程，BPE编码逐渐将常见的字符组合合并为新的字符，从而生成更加紧凑的表示形式。这样可以同时解决两个问题：数据压缩和词汇表示。对于数据压缩，常见的字符组合被合并为更短的编码，从而减少了数据的存储空间；对于词汇表示，BPE编码可以将不常见的词汇拆分成常见的字符组合，从而提高了模型对未知词汇的泛化能力。
2. Tokenizer：是自然语言处理中的一个术语，指的是将文本分割成更小单元，例如单词、子词或字符）的过程。Tokenizer可以将原始文本转换为模型可以理解和处理的输入形式。"Tokenization"是指将文本转换为令牌（tokens）的过程。
   1. "Tokenizer"可以是一种算法或工具，它根据特定的规则或模型将文本切分为令牌。一些常见的Tokenizer算法包括空格分割、基于规则的分割、正则表达式、最大匹配算法、BPE（Byte Pair Encoding）、WordPiece等。
   2. Tokenizer是预处理的关键步骤之一，它为模型提供了有效的输入表示形式。通过将文本转换为令牌，模型可以更好地理解句子的结构和语义，并能够处理不同长度和形式的文本。

## 解码流程
1. 解码流程
![](/img/in-post/post-ai/inference/fast-whisper/decoder/decoder-flow.png)   


2. 任务处理流程

![](/img/in-post/post-ai/inference/fast-whisper/decoder/process-flow.png)   

在处理流程中使用了更多的sample算法（例如top-k、top-n、tail-free、typical）、切割、合并、采样

## 为什么在opendomain领域不能使用beamsearch解码
在开放域（opendomain）领域，即处理广泛的话题和多样化的输入的场景中，使用传统的束搜索（beam search）解码方法可能会面临一些挑战和限制。
1. 多样性不足：束搜索方法在生成序列时候会保持固定数量的候选，这限制了生成结果的多样性。在开放域任务中，我们通常期望生成多样且丰富的输出，以应对不同的用户查询和语境。束搜索容易陷入局部最优解，导致生成的结果相似且缺乏创新。
2. 长距离依赖问题：在解码过程中，束搜索算法通常根据每个时间步的局部得分进行选择，这可能导致长距离的依赖关系被忽视。在开放域任务中，上下文的信息和全局一致性对于生成准确和连贯的输出至关重要。束搜索无法有效捕捉长距离依赖，导致生成结果可能出现不连贯或不准确的情况。
3. 限制搜索空间：束搜索通过设置束宽度来控制生成的候选数量，这样可以降低搜索空间的大小以提高效率。然而，在开放域任务中，搜索空间往往非常巨大，束宽度的限制可能导致无法覆盖潜在的正确答案，从而影响生成结果的质量。

为了克服这些问题，研究人员提出了一些改进束搜索的方法，如长度规范化束搜索（Length Normalization Beam Search）、采样解码（Sampling Decoding）、多样性增强方法（Diversity-Enhanced Decoding）等。这些方法试图增加生成结果的多样性、改善长距离依赖和扩展搜索空间，以更好地适应开放域任务的需求。

总而言之，在开放域领域中，束搜索解码方法的局限性可能导致生成结果缺乏多样性、连贯性和准确性。



## 参考文献
1. >[ LLama.cpp ](https://github.com/ggerganov/llama.cpp) 
2. >[ MIROSTAT  A NEURAL TEXT DECODING ALGORITHM THAT DIRECTLY CONTROLS PERPLEXITY](https://arxiv.org/pdf/2007.14966.pdf)