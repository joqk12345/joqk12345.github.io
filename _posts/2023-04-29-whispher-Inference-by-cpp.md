---
layout:       post
title:        "High-performance inference of OpenAI's Whisper"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - LLM
    - Inference
    - asr
    - Mixed F16/F32 precision
    - Low memory usage(Flash Attention)
    - Zero memory allocation at runtime
    - CPU first
---

> OpenAI 开源了whisper的语音大模型，支持多语言效果很惊艳，但是性能一直被诟病，主要是就是GPU推理慢、CPU的低成本方案更是慢，最近网上出现了一些解决方案，这个次的低成本方案比较有代表性，我们先来分析一下。后续google也会讲一下whisper-JAX，是谷歌的JAX加速推理方案。
> “Yeah It's on. ”
>  
>  

# whisper cpp
## Quick Start
  1.  首先我们找到whisper的GitHub仓库，[whisper.cpp](https://github.com/ggerganov/whisper.cpp)，克隆到本地；
   > git clone https://github.com/ggerganov/whisper.cpp.git
  2.  下载转换后的GGML模型
   > bash ./models/download-ggml-model.sh base.en
   > Downloading ggml model base.en from > 'https://huggingface.co/ggerganov/whisper.cpp' ...
ggml-base.en.bin            98%[====================================> ] 139.13M   631KB/s    eta 4s
   > 可以看到下载后经过转换后的模型大小仅仅14M
   41M Mar 22 16:04 ggml-base.en.bin
  3. 编译主函数
```
make
sysctl: unknown oid 'hw.optional.arm64'
I whisper.cpp build info:
I UNAME_S:  Darwin
I UNAME_P:  i386
I UNAME_M:  x86_64
I CFLAGS:   -I.              -O3 -DNDEBUG -std=c11   -fPIC -pthread -mf16c -mfma -mavx -mavx2 -DGGML_USE_ACCELERATE
I CXXFLAGS: -I. -I./examples -O3 -DNDEBUG -std=c++11 -fPIC -pthread
I LDFLAGS:   -framework Accelerate
I CC:       Apple clang version 13.0.0 (clang-1300.0.29.30)
I CXX:      Apple clang version 13.0.0 (clang-1300.0.29.30)

cc  -I.              -O3 -DNDEBUG -std=c11   -fPIC -pthread -mf16c -mfma -mavx -mavx2 -DGGML_USE_ACCELERATE   -c ggml.c -o ggml.o
c++ -I. -I./examples -O3 -DNDEBUG -std=c++11 -fPIC -pthread -c whisper.cpp -o whisper.o
c++ -I. -I./examples -O3 -DNDEBUG -std=c++11 -fPIC -pthread examples/main/main.cpp examples/common.cpp ggml.o whisper.o -o main  -framework Accelerate

```

执行demo
可以感受到整体耗时非常低,rtf为0.18与工业界asr模型的处理速度相当。
```
./main -f samples/jfk.wav
whisper_init_from_file_no_state: loading model from 'models/ggml-base.en.bin'
whisper_model_load: loading model
whisper_model_load: n_vocab       = 51864
whisper_model_load: n_audio_ctx   = 1500
whisper_model_load: n_audio_state = 512
whisper_model_load: n_audio_head  = 8
whisper_model_load: n_audio_layer = 6
whisper_model_load: n_text_ctx    = 448
whisper_model_load: n_text_state  = 512
whisper_model_load: n_text_head   = 8
whisper_model_load: n_text_layer  = 6
whisper_model_load: n_mels        = 80
whisper_model_load: f16           = 1
whisper_model_load: type          = 2
whisper_model_load: mem required  =  218.00 MB (+    6.00 MB per decoder)
whisper_model_load: adding 1607 extra tokens
whisper_model_load: model ctx     =  140.60 MB
whisper_model_load: model size    =  140.54 MB
whisper_init_state: kv self size  =    5.25 MB
whisper_init_state: kv cross size =   17.58 MB

system_info: n_threads = 4 / 12 | AVX = 1 | AVX2 = 1 | AVX512 = 0 | FMA = 1 | NEON = 0 | ARM_FMA = 0 | F16C = 1 | FP16_VA = 0 | WASM_SIMD = 0 | BLAS = 1 | SSE3 = 1 | VSX = 0 | COREML = 0 |

main: processing 'samples/jfk.wav' (176000 samples, 11.0 sec), 4 threads, 1 processors, lang = en, task = transcribe, timestamps = 1 ...


[00:00:00.000 --> 00:00:11.000]   And so my fellow Americans, ask not what your country can do for you, ask what you can do for your country.


whisper_print_timings:     load time =   210.45 ms
whisper_print_timings:     fallbacks =   0 p /   0 h
whisper_print_timings:      mel time =   111.38 ms
whisper_print_timings:   sample time =    21.23 ms /    27 runs (    0.79 ms per run)
whisper_print_timings:   encode time =  1457.12 ms /     1 runs ( 1457.12 ms per run)
whisper_print_timings:   decode time =   176.53 ms /    27 runs (    6.54 ms per run)
whisper_print_timings:    total time =  2030.53 ms
```

> 顺便提一句遇到不会的就会ChatGPT，

## Core ML support
1.  Apple Silicon devices 集成了 Apple Neural Engine (ANE) 。 Apple Neural Engine (ANE) 是集成在苹果硬件设备上的加速引擎，主要用于执行高效的神经网络任务，Core ML是苹果提供给开发者的开发框架主要是将机器学习模型集成到苹果的设备上。如果部署在苹果设备上的模型兼容了ANE，那么模型的推理会在ANE上执行，他的执行速度远远超过了传统的CPU执行；
 在本例子中，如果在苹果芯片上执行运算，那么whisper的encoder 的执行会在ANE上执行，他的推理速度是3x于cpu-only的执行。

2. 关于M1上的测试报告可以参看 [whisper-m1](https://github.com/ggerganov/whisper.cpp/pull/566),整体算下来会有16x加速比，这个在CPU的机器上已经很快了。


## 这个项目目前的缺点就是只支持cpu 推理，没有支持gpu 推理

## 实时识别
竟然也支持流程识别，但是我试了一下英文的效果一般
```
make stream
./stream -m ./models/ggml-base.en.bin -t 8 --step 500 --length 5000

```
## Confidence color-coding（置信度颜色编码）

Confidence color-coding是一种用于视觉化表示特定信息的置信度水平的技术，例如预测或测量。这种技术通常用于数据可视化或用户界面中，以帮助用户快速了解所呈现信息的质量。

在置信度颜色编码中，不同的颜色用于表示不同的置信度级别。例如，绿色可能用于表示高置信度，黄色用于表示中等置信度，而红色用于表示低置信度。所使用的确切颜色和阈值可能因应用程序和上下文而异。

```
./main -m models/ggml-base.en.bin -f samples/jfk.wav --print-colors
```
效果如下：
![](/img/in-post/post-ai/inference/whisper-inference-confidence-color-encoding.png)

## Karaoke-style movie  generator
Karaoke-style movie（卡拉OK式电影）是一种在屏幕上呈现歌词和音乐的电影。在这种电影中，歌曲的歌词通常会出现在屏幕底部，并配以歌曲的音乐。观众可以跟着歌词唱歌，就像在卡拉OK中唱歌一样.

```
/main -m ./models/ggml-base.en.bin -f ./samples/gb0.wav -owts
source ./samples/gb0.wav.wts
ffplay ./samples/gb0.wav.mp4
```

## ggml format

将原始的pytorch模型转换为一种自定义的2进制格式。并且把所需的所有文件打包成一个文件。
whipser的参数文件主要包括：
```
model parameters
mel filters
vocabulary
weights
```
关于模型转换脚本可以看这个[convert-pt-to-ggml](https://github.com/ggerganov/whisper.cpp/blob/master/models/convert-pt-to-ggml.py)

关于ggml的更多信息见附录3.

## Benchmarks
提供了 bench tool，可以对比不同模型的性能。

## other
1. 提供了Word-level timestamp的解码支持
2. 支持各种类型的bindings/Rust/JS/Go/ruby/object-c/.Net/python/R/unity
3. 常见的问题，见附录4.
4. 多样化的例子，例子主要是WASM的web应用

## wasm
wasm（WebAssembly）是一种二进制格式的文件，它是一种可以在现代Web浏览器中运行的虚拟机（VM）字节码。wasm文件包含编译后的代码和数据，可以在WebAssembly虚拟机中运行，这使得Web浏览器可以运行高性能和复杂的应用程序，如3D游戏、视频编解码器等。

wasm文件是一种非常紧凑的格式，可以快速加载和执行，这是它的一个重要特点。它还可以与多种编程语言一起使用，如C/C++、Rust、Python等。开发人员可以使用这些语言编写代码，然后将其编译为wasm格式，以在Web浏览器中运行。

wasm文件可以通过HTTP协议从Web服务器下载到客户端浏览器中，也可以通过JavaScript代码动态生成。在浏览器中运行wasm文件需要使用wasm解释器或JIT编译器。许多现代浏览器，如Chrome、Firefox、Safari等，已经支持wasm格式，使得开发人员可以使用这种格式构建高性能的Web应用程序。

## 性能篇
1.  Decoding strategies：[Improve decoding](https://github.com/ggerganov/whisper.cpp/pull/291)
2.  Memory usage reduction: 主要通过他的数据结构来支持[Memory usage reduction](https://github.com/ggerganov/whisper.cpp/issues/272)
3.  Core ML support:支持apple M1 芯片
4.  FP8 weights / 4-bit quantization,量化技术，[量化技术](https://github.com/ggerganov/ggml/pull/27)
5.  GPU support:ongoing
6.  Low-power mode：[Diminishing](https://github.com/ggerganov/whisper.cpp/issues/200#issuecomment-1337564222)

## 参考文件

1. >[ coreml ](https://developer.apple.com/documentation/coreml) 
2. >[pytorch model](https://datawhalechina.github.io/thorough-pytorch/%E7%AC%AC%E4%BA%94%E7%AB%A0/5.4%20PyTorh%E6%A8%A1%E5%9E%8B%E4%BF%9D%E5%AD%98%E4%B8%8E%E8%AF%BB%E5%8F%96.html)
3. >[ggml](https://github.com/ggerganov/ggml)
4. >[FAQ](https://github.com/ggerganov/whisper.cpp/discussions/126)
5. >[ggml-document](https://github.com/ggerganov/whisper.cpp/issues/40)
