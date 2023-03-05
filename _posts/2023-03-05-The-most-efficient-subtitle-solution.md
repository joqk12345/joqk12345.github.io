---
layout:       post
title:        "The most efficient subtitle solution."
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - whisper 
    - auto transcribe 
---

# whisper-auto-transcribe 

使用whisper做自动翻译并生成字幕(subtitle),并使用iina播放器播放。

## 安装whisper
1. 参考地址： https://github.com/openai/whisper
2. pip3 install git+https://github.com/openai/whisper.git 
3. 安装过程发现缺少一个矩阵运算的库：
    sudo apt-get install libatlas-base-dev
4. 安装ffmpeg：sudo apt update && sudo apt install ffmpeg
5. 安装完成后的效果

![](/img/in-post/post-ai/auto-transcriber/whisper-showcase01.png)


## 安装whisper-auto-transcribe
1. https://github.com/tomchang25/whisper-auto-transcribe
2. git clone https://github.com/tomchang25/whisper-auto-transcribe.git
3. pip install gradio
4. Open application: python gui.py or use the command line:
```
python3 ./cli.py ../../test_data/cn/pie/pie-ep46.mp3  --output "../../test_data/cn/pie/pie-ep46.srt" --task transcribe --model large
```
5. 执行展示效果

![](/img/in-post/post-ai/auto-transcriber/showcase-02.png)

6. 转录字幕效果



## 安装iina,a player based upon MPV

1. 安装： 
```
brew install iina
```
2. 配置纯音频与字幕后发现不显示字幕，进而发现bug
https://github.com/iina/iina/issues/972 代开发者修复




