---
layout: post
title: Anaconda安装tensorflow(macos)
date: 2018-01-26
categories: blog
tags: [ml, python]
description:
---

## 安装Anaconda
- 什么是anaconda？

一个python发行版，主要用于科学计算

anaconda之于python，类似于ubuntu/centos之于linux

- 安装

anaconda官网下载安装包(ui命令行都行)，直接双击安装

## 使用anaconda安装tf

- 创建conda env: tensorflow

命令：
``` java
conda create -n tensorflow python=3.6
/*
有可能会提示找不到conda命令(别问我为什么知道)
其实anaconda安装程序已经把环境变量添加到了.bash_profile里，只要手动执行以下source ~/.bash_profile即可
如果不想每次启动终端都执行source命令，可以编辑.zshrc(视终端类型而定)，再最后一行加上source ~/.bash_profile
*/

```

- 激活env

```
source activate tensorflow
```

- pip安装tf
```
pip install --ignore-installed --upgrade TF_PYTHON_URL
```
TF_PYTHON_URL见：[url of the tf python package](https://www.tensorflow.org/install/install_mac#the_url_of_the_tensorflow_python_package)

- 验证安装

``` python
# Python
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```