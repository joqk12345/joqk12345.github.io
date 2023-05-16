---
layout:     post
title:      "whisper-jax"
subtitle:   ""
date:       2023-05-15 10:52:00
author:     "Galaxies"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - jax
    - xla
---

> 为OpenAI的Whisper模型优化的JAX代码，主要建立在huggingfacede的Transformer的Whisper实现之上。与OpenAI的PyTorch代码相比，Whisper JAX的运行速度提高了70倍以上，成为目前最快的Whisper实现。
>jax，mojo有两个有意思的点，一个是内置simd支持，另一个是ml ir
> “Yeah It's on. ”
>  
>  

## whisper-jax使用

1. 创建开发环境
   1. conda create --name whisper-jax python=3.9
2. 激活开发环境
   1. conda activate whisper-jax
3. 
4. 
5. 安装
```
pip install --upgrade --no-deps --force-reinstall git+https://github.com/sanchit-gandhi/whisper-jax.git
```

1. 使用测试代码

```
from whisper_jax import FlaxWhisperPipline

# instantiate pipeline
pipeline = FlaxWhisperPipline("openai/whisper-large-v2")

# JIT compile the forward call - slow, but we only do once
text = pipeline("pie-ep62.mp3")

# used cached function thereafter - super fast!!
text = pipeline("pie-ep62.mp3")
```


缺少flax库
1. 升级Transformer版本为4.29.1
   1.  pip install -i https://pypi.tuna.tsinghua.edu.cn/simple  transformers==4.29.1
2. pip install flax
   1. Flax: A neural network library and ecosystem for JAX designed for flexibility

3. 执行推理
   ![](/img/in-post/post-ai-framework/jax/whisper-jax/inference.png)   

### 在GPU(T4)上执行推理任务的报错以及解决方案
1. 执行报WARNING - No GPU/TPU found, falling back to CPU.
   1. 需要安装jax_cuda+releases.html
   2. pip install -U jax[cuda11_cudnn82] -f https://storage.googleapis.com/jax-releases/jax_cuda_releases.html
2. 执行
   1.  CUDA_VISIBLE_DEVICES=1 python wispher_jax_test.py
3. 执行效果
   1. ![](/img/in-post/post-ai-framework/jax/whisper-jax/inference.png)   
## 参考文献
1. >[ A machine learning compiler ](https://github.com/openxla/xla) 
2. >[ flax ](https://pypi.org/project/flax/)
3. >[ google jax ](https://github.com/google/jax/discussions/10323)
