---
layout:       post
title:        "Know Sth About LangChain"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - langChain
    - AI
    - ChatGPT
    - Application
 
---

# LLM adhesive layer

## 基本知识
* Token：这个指的是 OpenAI 处理文本的基本单位，可以是单词或者字符的片段。例如，“hamburger” 被分成 “ham”、“bur” 和 “ger” 三个 Token，而 “pear” 是一个 Token。1个 Token 大约相当于 4 个字符或者 0.75 个英文单词。
* 限制
  1. OpenAI 的模型有一个固定的 Token 限制，例如 GPT-3 的 Davinci 模型最多可以处理2049 个 Token，大约 1500 个英文单词。最新 Turbo 模型大约是 4,096 个 Token，大约是 3000 个英文单词。
  2. Token 限制的计数包含输入和输出的文本。换句话说，不是说我输入 3000 个英文单词，然后 OpenAI 能返回 3000 个英文单词的结果，而是输入+输出总共不能超过 3000 个英文单词。
  3. 文字限制。目前 OpenAI 的 GPT-3 模型只能处理文字。
* prompt
  * 给AI模型的指令，可以是一个问题、一段文字描述，甚至可以是带有一堆参数的文字描述。AI 模型会基于 prompt 所提供的信息，生成对应的文本，亦或者图片。
* Prompt Engineering （PE) 
   * Prompt Engineering 是一种人工智能（AI）技术，它通过设计和改进 AI 的 prompt 来提高 AI 的表现。Prompt Engineering 的目标是创建高度有效和可控的 AI 系统，使其能够准确、可靠地执行特定任务。
   * 如果你只想让 AI 给你答案，你不需要额外做什么，只需要输入文字即可。但如果你想要得到满意的答案，甚至精确的答案。就需要用到 PE 这个技术。
     1.  因为人类的语言从根本上说是不精确的，目前机器还没法很好地理解人类说的话，所以才会出现 PE 这个技术。另外，受制于目前大语言模型 AI 的实现原理，部分逻辑运算问题，需要额外对 AI 进行提示
     2.  目前的 AI 产品还比较早期，因为各种原因，产品设置了很多限制，如果你想要绕过一些限制，或者更好地发挥 AI 的能力，也需要用到 Prompt Engineering 技术。
  * 总的来说，Prompt Engineering 是一种重要的 AI 技术 
    * 如果你是 AI 产品用户，可以通过这个技术，充分发挥 AI 产品的能力，获得更好的体验，从而提高工作效率。
    * 如果你是产品设计师，或者研发人员，你可以通过它来设计和改进 AI 系统的提示，从而提高 AI 系统的性能和准确性，为用户带来更好的 AI 体验。

## What is LangChain？ 
LangChain is a framework for developing applications powered by language models. We believe that the most powerful and differentiated applications will not only call out to a language model via an API, but will also:
* Be data-aware: connect a language model to other sources of data
* Be agentic: allow a language model to interact with its environment
The LangChain framework is designed with the above principles in mind.


# 

## 使用langchain 生成内容摘要






## 参考文件

1.  > [ langchain-tutorials ]
   (https://github.com/gkamradt/langchain-tutorials) 

2.  > [ langchain-document ]
   (https://github.com/gkamradt/langchain-tutorials) 
   
3. > [AI Summary vs 人工 Summary]
   (https://learningprompt.wiki/docs/insight/AI%20Summary%20%E4%BC%9A%E5%8F%96%E4%BB%A3%E4%BA%BA%E5%B7%A5%20Summary%20%E5%90%97%EF%BC%9F)
