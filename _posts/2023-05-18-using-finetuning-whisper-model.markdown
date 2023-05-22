---
layout:     post
title:      "fast-whisper"
subtitle:   "一种经过微调之后且加速的识别模型"
date:       2023-05-18 16:30:00
author:     "Galaxies"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - fast-whisper
    - fune-tunning
    - lora
---

> whisper网上各种加速方案，之前介绍过whisper.cpp的方案，这次介绍一下fast-whisper方案。他使用了CTranslate2的推理引擎，好处。。
> LoRA技术的发展使得大模型的finetuning成本越来越低，所以导致出现了很多基于whisper的大模型finetunning模型，这次我们带来基于ai-shell数据集的whisper-finetunning版本的中文ASR训练，目前看来识别率比原始的识别率有大幅降低。
> “Yeah It's on. ”
>  
>  

## fast-whisper

### fast-whisper是什么
fast-whisper是基于Ctranslate2推理引擎。而Ctranslate2是一个底层是C++实现的对Transformer模型进行推理的库，该项目的好用的地方在于采用了Data Scientist 喜欢的方式依赖少而且只暴露的简单的python APIs 和 C++ API 非常方便的集成。
* [Ctranslate2 CPP API](https://github.com/OpenNMT/CTranslate2/tree/master/python/cpp)

Ctranslate2还支持很多模型，这里可以看到Ctranslate2支持的模型列表:
* [Ctranslate2 Support Models](https://github.com/OpenNMT/CTranslate2/tree/master/src/models)

他实现了一个定制的运行时间，应用了许多性能优化技术，如权重量化、图层融合、批量重排等，以加速和减少Transformer模型在CPU和GPU上的内存使用。


### 为什么要选择使用fast-whipser？
1. CTranslate2 支持多种模型类型
包括encoder-decoder模型(Encoder-decoder models: Transformer base/big, M2M-100, NLLB, BART, mBART, Pegasus, T5, Whisper) 和decoder-only( GPT-2, GPT-J, GPT-NeoX, OPT, BLOOM)模型。
如果关注LLama的同学可以参考这issues： https://github.com/OpenNMT/CTranslate2/issues/1127

2. 他支持多种深度学习框架
包括OpenNMT-py、OpenNMT-tf、Fairseq、Marian、OPUS-MT、Transformers，并将他们转换成优化之后的模型格式（optimized model format）。

3. 多device支持
支持在CPU和GPU设备上地资源高效执行。

4. 量化和减少精度
支持的精度类型16-bit floating points (FP16), 16-bit integers (INT16), and 8-bit integers (INT8).

5. 支持多种CPU架构
该项目支持x86-64和AArch64/ARM64处理器，并集成了多个为这些平台优化的后端： Intel MKL、oneDNN、OpenBLAS、Ruy和Apple Accelerate。

6. 自动CPU检测和代码调度
一个二进制文件可以包括多个后端（如英特尔MKL和oneDNN）和指令集架构（如AVX、AVX2），在运行时根据CPU信息自动选择。

7. 并行和异步执行

[Try]多batch执行、可以使用多GPU或CPU内核进行并行和异步处理。

8. 动态内存

由于CPU和GPU上的缓存分配器，内存使用量根据请求大小动态变化，同时仍然满足性能要求。

9.  可配置的解码策略

先进的解码功能允许自动完成部分序列，并在序列中的特定位置返回备选方案。具体可以参考附件4.


## model-conversation
1. 安装依赖库

```
   pip3 install ctranslate2
   pip3 install transformers==4.29.2
```

2. 模型转换并量化

``` 做fp16 量化
  ct2-transformers-converter --model ./whisper-medium --output_dir whisper-large-v2-ct2 \
    --copy_files tokenizer.json --quantization float16
```

```in8量化
    ct2-transformers-converter --model ./whisper-medium --output_dir whisper-medium-v2-int8-ct2     --copy_files tokenizer.json --quantization int8
```

3. 量化后的模型大小

 ```
   1.5G	whisper-large-v2-ct2
   2.9G	whisper-medium
   752M	whisper-medium-v2-int8-ct2
 ```

4. 模型推理与参数选择

量化后的模型可以使用新的量化模型进行推理，推理的时候需要注明使用的device(cpu/cuda)、量化参数(fp16/int8).
* 模型加载
![](/img/in-post/post-ai/inference/fast-whisper/load_model.png)   

* 模型解码策略配置
![](/img/in-post/post-ai/inference/fast-whisper/decoding-strategy.png)   



## 参考文献
1. >[ faster-whisper ](https://github.com/guillaumekln/faster-whisper) 
2. >[ Whisper-Finetune ](https://github.com/yeyupiaoling/Whisper-Finetune)
3. >[ quantization ](https://opennmt.net/CTranslate2/quantization.html)
4. >[ decoding features ](https://opennmt.net/CTranslate2/decoding.html)
