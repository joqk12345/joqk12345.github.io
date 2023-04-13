---
layout:       post
title:        "Know Sth About Poetry"
author:       "galaxies"
header-style:  text
catalog:      true
tags:
    - python
    - poetry
    - dependency manage
 
---

## 背景
最近在做Python项目，一直在寻找一个类似JAVA Maven的一个包管理工具去隔离整个项目，Python项目的项目结构、包管理、依赖管理、编译执行、虚拟环境比较原始，如果多人协作一些项目容易出现很多不一致的问题，现代开发依赖管理是必备的工具，终于在一个开源项目langchain发现了poetry，用起来还挺好用，就打算介绍一下给写Python的同学们。

## 如何使用
1. 安装poetry

linux&MacOS 安装

```
curl -sSL https://install.python-poetry.org | python3 -

```

2. 创建环境

* 新项目创建
```
poetry new poetry-demo
```

* 已有项目创建

```
cd pre-existing-project
poetry init

```


1. 创建虚拟机

```
(content-generators-py3.9) (base) ➜  content-generators git:(dev/qiaokai) ✗  poetry shell
Virtual environment already activated: /Users/kai.qiao/Library/Caches/pypoetry/virtualenvs/content-generators-jXvlB5kq-py3.9

```
3. 安装包依赖

```
(content-generators-py3.9) (base) ➜  content-generators git:(dev/qiaokai) ✗ poetry add pysrt@1.1.2

Updating dependencies
Resolving dependencies... (2.1s)

Writing lock file

Package operations: 2 installs, 0 updates, 0 removals

  • Installing chardet (5.1.0)
  • Installing pysrt (1.1.2)
```

4. 执行测试脚本

```
 poetry run pytest tests
 ================ test session starts ==============
platform darwin -- Python 3.9.7, pytest-7.3.0, pluggy-1.0.0
rootdir: /Users/kai.qiao/workspace/Py_Workspace/content-generators
collected 1 item                                                                                                                                    
tests/unit_tests/data_loader/test_youtube_dataloader.py .                                                                                                                                                                        [100%]

================= 1 passed in 0.02s ===================

```




## 

## 






## 参考文件

1.  > [ langchain-tutorials ]
   (https://python-poetry.org/docs/basic-usage/) 
2. >[ poetry 常用命令 ](https://python-poetry.org/docs/cli/)
