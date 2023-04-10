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


## langchain 的主要模块
1. Models
   1. 文档的这一部分涉及 LangChain 中使用的不同类型的模型。在此页面上，我们将在较高层次上介绍模型类型，但我们为每种模型类型提供了单独的页面。
   2. 模型列表
      1. LLMs：大型语言模型 (LLM) 是我们涵盖的第一类模型。这些模型将文本字符串作为输入，并返回文本字符串作为输出
      2. Chat Models：这些模型通常由语言模型支持，但它们的 API 更加结构化。具体来说，这些模型将聊天消息列表作为输入，并返回聊天消息。
      3. Text Embedding Models：这些模型将文本作为输入并返回一个浮点数列表。
2. Prompts
   1. 编程模型的新方法是通过提示。 “提示”是指模型的输入。此输入很少被硬编码，而是通常由多个组件构成。PromptTemplate 负责构建此输入。LangChain 提供了几个类和函数来简化提示的构建和使用。
   2. 分类
      1. LLM Prompt Templates：如何使用 PromptTemplates 提示语言模型。
      2. Chat Prompt Templates：如何使用 PromptTemplates 提示聊天模型。
      3. Example Selectors：通常，在提示中包含示例很有用。这些示例可以硬编码，但如果它们是动态选择的，通常会更强大。本节介绍示例选择。
      4. Output Parsers：语言模型（和聊天模型）输出文本。但很多时候，您可能希望获得更多的结构化信息，而不仅仅是文本回复。这就是输出解析器发挥作用的地方。输出解析器负责 (1) 指示模型如何格式化输出，(2) 将输出解析为所需的格式（包括在必要时重试）。
3. Indexes
   1. 索引指的是构建文档的方式，以便 LLM 可以最好地与它们交互。该模块包含用于处理文档、不同类型索引的实用函数，以及在链中使用这些索引的示例。
   2. 在Langchain中使用索引的最常见方式是在“检索”步骤中。此步骤指的是接受用户的查询并返回最相关的文档。
   3. 我们做出这种区分是因为 (1) 索引除了检索之外还可以用于其他用途，（2）检索可以使用除索引之外的其他逻辑来查找相关文档。因此，我们有一个“检索器”接口的概念——这是大多数langchain使用的接口。
   4. 大多数时候，当我们谈论索引和检索时，我们谈论的是索引和检索非结构化数据（如文本文档）。要与结构化数据（SQL 表等）或 API 进行交互，请参阅相应的用例部分以获取相关功能的链接。LangChain 支持的主要索引和检索类型目前以矢量数据库为中心，因此我们深入研究了这些主题的许多功能。
4. Memory
   1. 默认情况下，Chains 和 Agents 是无状态的，这意味着它们独立地处理每个传入的查询（底层 LLM 和聊天模型也是如此）。在某些应用程序（聊天机器人是一个很好的例子）中，记住以前的交互非常重要，无论是短期的还是长期的。 “内存”的概念正是为了做到这一点而存在的。
5. Chains
   1. 单独使用 LLM 对于一些简单的应用程序来说很好，但许多更复杂的应用程序需要链接 LLMs - 彼此或与其他专家链接。LangChain 提供了 Chains 的标准接口，以及一些常见的 Chain 实现，方便使用。
   2. langchain提供了很多chain的实现
      1. LLM Chain/Sequential Chains/……
6. Agents（代理）
   1. 个人助理的特点
      1. 他们能够和外部世界交互-Agent Documentation (for interacting with the outside world)
      2. 他们知道你的数据-Index Documentation (for giving them knowledge of your data)
      3. 他们能记住你的交互-Memory (for helping them remember interactions)
   2. 代理
      1. 某些应用程序不仅需要预先确定的对 LLM/其他工具的调用链，还可能需要依赖于用户输入的未知链。在这些类型的链中，有一个“代理”可以访问一套工具，根据用户输入，代理可以决定调用这些工具中的哪一个（如果有的话）。
   3. 模块划分
      1. Tools：各种工具（技能）
      2. Agents： 不通类型的代理
      3. Toolkits：工具包
      4. Agent Executor： 代理执行器
## 使用langchain 生成内容摘要






## 参考文件

1.  > [ langchain-tutorials ]
   (https://github.com/gkamradt/langchain-tutorials) 

2.  > [ langchain-document ]
   (https://github.com/gkamradt/langchain-tutorials) 
   
3. > [AI Summary vs 人工 Summary]
   (https://learningprompt.wiki/docs/insight/AI%20Summary%20%E4%BC%9A%E5%8F%96%E4%BB%A3%E4%BA%BA%E5%B7%A5%20Summary%20%E5%90%97%EF%BC%9F)
