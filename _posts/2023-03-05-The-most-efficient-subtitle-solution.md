---
layout:       post
title:        "The most efficient subtitle solution"
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
python3 ./cli.py ../../test_data/cn/pie/pie-ep46.mp3  --output "../../test_data/cn/pie/pie-ep46.srt" --task transcribe --model large --device cuda:1
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

3. 自己通过给mp3加id3头信息并配上封面之后就可以了

4. 另一种方案可以采用FFmpeg将mp3转mp4 也是可以播放字幕的

5. audio配置字幕的效果
![](/img/in-post/post-ai/auto-transcriber/audio_with_srt.png)

6. video配字幕的效果
![](/img/in-post/post-ai/auto-transcriber/video_with_srt.png)


## FAQ
1. 遇到的问题一直加载不了字幕的问题
    其实遇到的最大的问题就是IINA工具的问题，一定要确保从官网上下载，不要使用brew install 因为这个不一定是最新版的，重新安装之后加载字幕就正常了
2. 用于测试的mp4的资源
    https://gist.github.com/jsturgis/3b19447b304616f18657

3. 给mp3加ID3头的代码

```
from mutagen import File
from mutagen.mp3 import MP3
from mutagen.id3 import ID3, APIC, TIT2, TPE1, TALB
def set_mp3_info(path,info):


    songFile = MP3(path)
    if songFile.tags is None:
        songFile.add_tags()
    # // 插入封面
    songFile['APIC'] = APIC(
        encoding=3,
        mine='image/jpeg',
        desc=u'Cover',
        data=info['picData']
    )
    # 插入歌名
    songFile['TIT2'] = TIT2(
        encodings = 3,
        text =info['title']
    )
    #  插入第一演奏家、歌手、等
    songFile['TPE1'] = TPE1(
        encodings = 3,
        text = info['artist']
    )
    #  插入专辑名
    songFile['TALB'] = TALB(
        encodings =3,
        text = info['album']
    )
    songFile.save()

if __name__ == '__main__':
    MP3_file = "~/pie/pie-ep46.mp3"
    # 读取封面图片
    with open('~/pie/cover/cover.png', 'rb') as f:
        picData = f.read()

    info = {
        'picData':picData,
        'title':'后互联时代的乱弹',
        'artist':'李俊、庄表伟、王伟',
        'album':'pie'
    }

    set_mp3_info(MP3_file,info)
```
