---
layout:       post
title:        "LLAMA Inference"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - LLM
    - Inference
    - pyllama
    - distributred Model Inference
    - torchrun
---

> Meta 开源了 LLaMA（Large Language Model Meta AI） 预训练模型，衍生出了一系列二次开发场景，都基于 LLaMA finetune，他的子孙包括Alpaca、Vicuna、Koala、Baize、Luotuo
> “Yeah It's on. ”
>  
>  

# LLM 推理
  1.  Meta开源Meta 开源了 LLaMA（Large Language Model Meta AI） 预训练模型,我们找了一个基于llam的包装项目进行了一些尝试。llama 主要基于的技术：torch、fairscale、fire。
  2. 当前 7B 模型是 1 个分片，13B 模型是 2 个分片，30B 模型是 4 个分片，65B 模型是 8 个分片
  3. LLaMA 使用 Facebook 的 FairScale 训练，保存的模型为已分片后的模型，推理时分别加载即可，使用 torchrun 即可直接加载推理，不过有个问题需要解决：
     1. 端口绑定
         * 每个进程绑定不同的端口同时提供服务
         * 多个进程使用 reuse_port 绑定到同一端口，由内核控制负载均衡
         * 只有某个进程（rank）绑定端口，其他进程不绑定
      2. 数据同步问题，
         1. 模型并行需要保证每个进程输入同样的数据，因此在某个进程（rank）接收到数据后需要将数据同步到其他rank，然后一起推理
      3. 如果服务是以 demo 方式存在，可能会在较长时间没有流量，而为了保持不同进程间数据同步，NCCL 会一直处于监听状态（默认 timeout 为 30分钟），可能会导致超时退出，需要增加相应 timeout

## 模型地址提供
有一个[ipfs](https://ipfs.io/ipfs/Qmb9y5GCkTG7ZzbBWMu2BXwMkzyCKcUjtEKPpgdZ7GEFKm/)网站，提供了lama的7B/13B/30B/65B的四类模型可以通过wget下载。

## 使用pyllama库测试
1. 这是有人将lamma包装了一下用于国内使用
> pip3 install pyllama -U -i https://pypi.tuna.tsinghua.edu.cn/simple
2. pip3 install transformers
3. 执行模型下载
> python -m llama.download --model_size 7B 

## 执行模型推理
> python inference.py --ckpt_dir /xx/llm/7B --tokenizer_path /xx/llm/tokenizer.model
结果展示：
![](/img/in-post/post-ai/inference/llama-inference-1.png)

GPU使用率
![](/img/in-post/post-ai/inference/llama_inference.jpg)

##  start gradio webui
1.  pip3 install gradio
2.  python webapp_single.py  --ckpt_dir /xx/llm/7B --tokenizer_path /xx/llm/tokenizer.model

## Multiple Gpu Inference

1. torchrun --nproc_per_node MP example.py --ckpt_dir $TARGET_FOLDER/model_size --tokenizer_path $TARGET_FOLDER/tokenizer.model

2. 原生的pt模型推理较慢13b的模型单个query耗时在2s左右；需要考虑量化、蒸馏、增加batch大小

![](/img/in-post/post-ai/inference/llama-inference-13b.png)

## 量化(蒸馏)
TODO
## Batch


## 参考文件

1. >[ alpace ](https://github.com/tatsu-lab/stanford_alpaca) 
2. >[LLaMA及其子孙模型概述](https://juejin.cn/post/7220985690795851836)
3. >[llama](https://github.com/facebookresearch/llama)
4. >[GPTQ](https://github.com/qwopqwop200/GPTQ-for-LLaMa)
5. >[pyllama](https://github.com/juncongmoo/pyllama)
6. >[tensor_parallel](https://github.com/BlackSamorez/tensor_parallel)
