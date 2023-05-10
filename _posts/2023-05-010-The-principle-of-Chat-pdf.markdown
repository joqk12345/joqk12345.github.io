---
layout:     post
title:      "The principle of Chat pdf"
subtitle:   "semantic similarity"
date:       2023-05-04 22:11:00
author:     "Galaxies"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - semantic similarity
    - embedding
    - fine-tuning
    - cosine similarity
---


>
>  GPT擅长回答问题，但是只能回答它以前被训练过的问题，如果是没有训练过的数据，比如一些私有数据或者最新的数据该怎么办呢？这种情况下通常有两种办法，一种是微调（fine-tuning），一种是嵌入（embedding）。
> 微调就是在大模型的数据基础上做二次训练，事先准备好一批prompt-complition（类似于问答Q&A）的数据，生成新的模型，这个模型将会包含微调后的数据。而嵌入则是每次向ChatGPT发送消息（prompt）的时候，把你自己数据结果带上。
> ChatGPT就像一个已经训练好的家政阿姨，她懂中文，会做家务，但是对你家里的情况不了解。
> 微调就相当于阿姨第一次到你家干活的时候，你要花一小时时间告诉她家里的情况，比如物件的摆放、哪些地方不能动，哪些地方要重点照顾。
> 嵌入就相当于你省去了对阿姨进行二次培训的，而是在家里贴满纸条，这样阿姨一看到纸条就知道该怎么做了。既然他们这么有意思，那我们就开始探索吧。
> “Yeah It's on. ”
>  
>  

## Vector Database & Embedding

矢量数据库和嵌入是当前人工智能领域的热门话题。Pinecone是一家矢量数据库公司，刚刚以约10亿美元的估值筹集了1亿美元。Shopify、Brex、Hubspot和其他公司在其人工智能应用程序中使用它们。但它们是什么，它们如何工作，为什么它们在人工智能中如此重要？

让我们来了解一下。
![](/img/in-post/post-ai/application/db/book-embeddings.jpeg)   

### what are vector embeddings

Embeddings are just a N-dimensional vectors of numbers. They can represent anything, text, music, videos, etc. We'll focus on text。
> 嵌入只是一个N维的数字向量。它们可以代表任何东西，文本、音乐、视频等等。我们将专注于文本.

![](/img/in-post/post-ai/application/db/Embedding-model.jpeg)   

The process of creating an embedding is straight forward. It involves an embedding model (ex: Ada from Openai).
> 创建一个嵌入的过程是直接的。它涉及一个嵌入模型（例如：Openai的Ada）。

You send your text to the model, and it creates a vector representation of that data for you, which can be stored and used later on.
> 你把你的文本发送给模型，它为你创建一个数据的矢量表示，它可以被存储并在以后使用。

They’re important because they give us the power of semantic search, which is just means searching by similarity. Like by the meaning of the text.
> 它们很重要，因为它们给了我们语义搜索的力量，这只是意味着通过相似性进行搜索。比如通过文本的含义。

So in this example we can model a man, king, woman and queen on a vector plane and very easily see their relation to one another.
> 因此，在这个例子中，我们可以在一个矢量平面上建立一个男人、国王、女人和女王的模型，并非常容易地看到他们之间的关系。

![](/img/in-post/post-ai/application/db/embedding-demo.jpeg)   

Here's a slightly more straight forward example: 
Imagine you're a kid with a big box of toys. Now you want to find toys that are kind of the same, like a toy car and toy bus. 
They're both vehicles, so they're similar.

> 这里有一个稍微直接一点的例子： 想象一下，你是一个有一大箱玩具的孩子。现在你想找一些有点相同的玩具，比如玩具车和玩具巴士。它们都是车辆，所以它们是相似的。

That is called "semantic similarity". - When things have kinda similar meaning/idea.
Now lets say you have two toys which are related, but aren't the same. Like a toy car and a Toy road. 
They aren't the same, but do belong together cause cars generally drive on the road.
> 这被称为 "语义相似性"。- 当事物具有某种类似的意义/想法时。现在让我们说说你有两个相关的玩具，但并不一样。比如一辆玩具车和一条玩具路。它们不一样，但确实属于一起，因为汽车通常在路上行驶。

So, WHY ARE THEY SO IMPORTANT? 
Well it's because of the context limits LLM's have.
In an ideal world, we would be able to fit infinite number of words into an LLM prompt.
But as many of you know, we cant. Right now its limited to ~4096 - 32k tokens
> 那么，它们为什么如此重要呢？这是因为LLM上下文的背景限制。在一个理想的世界里，我们能够在LLM的提示中容纳无限多的词语。但正如你们中的许多人所知，我们做不到。现在，它被限制在~4096 - 32k tokens。

So we're severely limited to howe can interact with an LLM due to its "memory" aka how many words we can fit into its token limit.
It’s why you can’t copy paste a PDF into chatGPT and ask it to summarize it. (well maybe you can now cause gpt4-32k)
> 因此，由于LLM的 "内存"，也就是我们能在其标记限制中容纳多少字，我们与LLM的互动方式受到了严重限制。这就是为什么你不能把PDF复制粘贴到chatGPT中并要求它进行总结。(也许你现在可以，因为gpt4-32k）。

Now how does this all finally piece together? 
Well we can use vector embeddings to our advantage to inject the relevant text into an LLM context window. Lets take a look at an example:
> 现在，这一切是如何最终拼凑起来的呢？
我们可以利用矢量嵌入的优势，将相关文本注入LLM的上下文窗口。
上下文窗口中仅包含相关的文本内容，这样就不会超过tokens的长度限制。
让我们来看看一个例子：

Say you have a giant PDF, maybe one of a congressional hearing (heh)
And you're lazy so you dont want to read the whole thing, and you can't paste the whole thing cause its a billion pages long.
Perfect use-case for embeddings.
> 假设你有一个巨大的PDF文件，也许是国会听证会的文件（呵呵）。
你很懒，所以你不想读完整个文件，而且你不能粘贴整个文件，因为它有10亿页长。
这就是嵌入的完美用例。

So you take the PDF, and create a vector embedding, and store it in a database. Now you ask a question, "What did they say about xyz"
We first: Create an embedding of your question: "What did they say about xyz"
> 所以你拿着PDF，创建一个矢量嵌入，
> 你可以把 PDF 文件的文本内容先分块，然后借助Embedding把一块块文本变成一个个向量数组，并将其存储在向量数据库中。
> 在存储分块的向量数组时，通常还需要把向量数组和文本块之间的关系一起保存，这样后面我们按照向量检索出相似的向量数组后，能找出对应的文本块，一个参考的数据结构类似于这样：
```
{
[1,2,3,34]: '文本块1',
[2,3,4,56]: '文本块2',
[4,5,8,23]: '文本块3',
}。
```
> 现在你问一个问题，"他们对xyz说了什么"我们首先： 创建一个你的问题的嵌入： "他们对xyz是怎么说的"

Now we have two vectors: your question [1,2,3] and the pdf [1,2,3,34]
Then we compare the question vector against our giant PDF vectors using a similarity search. The recommended one from openai is the cosine similarity.
> 现在我们有两个向量：你的问题[1,2,3]和PDF[1,2,3,34] 。
然后我们用相似性搜索来比较问题向量和我们的巨型PDF向量。openai推荐的是余弦相似度。

![](/img/in-post/post-ai/application/db/cosine-similarity.png)   

3 of the most relevant embeddings and the text
We can now use the output of the 3 and feed it into an LLM with a little bit of prompt engineering.
> 3个最相关的嵌入和文本
我们现在可以使用3的输出，并通过一点提示工程将其送入LLM。
> 我们现在可以使用这3个文本块，连同prompt一起输入到 LLM 中。例如：

```
已知我们有上下文：文本块1，文本块2，文本块3。
现在有用户的问题：他们对 xyz 说了什么？
请根据给定的上下文，如实回答用户的问题。
如果你不能回答，那么如实告诉用户“我不知道”
```

The most common one is:
Given the context, answer the users question truthfully.
If you are not able to, then say “I cannot answer the question” and thats it!The LLM takes the relevant text chunks from your PDF and tries to truthfully answer your question.
> 最常见的是：根据上下文，如实回答用户的问题。如果你做不到，就说 "我不能回答这个问题"，然后就可以了！LLM从你的PDF中获取相关的文本块，并试图如实回答你的问题。

That's the super basic explanation of how embeddings and LLMs can give you pretty insane chat-like capabilities for any form of data.
It's also how all those "chat with your site/pdf/blah blah" work! 
THEY ARE NOT NOT FINE TUNING. That is just a marketing term.
> 这就是关于嵌入和LLMs如何为任何形式的数据提供类似于聊天的疯狂能力的超级基本解释。这也是所有那些 "与你的网站/pdf/blah blah聊天 "的工作原理！它们不是微调！他们并不是没有进行微调。这只是一个营销术语。


## fine-turing vs embedding
微调就像你通过学习准备考试，是一种长期记忆，但过了一周后考试来临，模型可能会忘记袭击，或者记错它从来没有读过的事实。
嵌入就像记笔记，是一种短期记忆，当考试的时候，你把笔记带上，随时翻看笔记，对于笔记上有的内容可以得到准确的答案。


## 参考文献
1. >[ openai-cookbook ](https://github.com/openai/openai-cookbook) 
2. >[ Vector databases & embeddings ](https://twitter.com/SullyOmarr/status/1655626066331938818) 