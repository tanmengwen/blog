---
layout: post
title:  "conda--安装与基本使用"
date:   2018-4-28 10:00:00 -0400
tag: cv
---

## Miniconda installer for Linux

- [下载地址](https://conda.io/miniconda.html)
- conda有python3.x和python2.x系列两个版本，其实都没有关系，因为你在使用conda进行创建环境时，可以指定python的版本。

## conda基本操作

### 常用命令

```
conda list     #查看已经安装的package
conda env list #查看指定某环境下安装的package
conda info -e  #查看当前系统下的环境
conda update conda #检查更新当前conda
```

### 创建使用新的环境

```
#指定python版本为2.7，注意至少需要指定python版本或者要安装的包，后一种情况下，自动安装最新python版本
#env_name是虚拟环境名，env_name文件可以在conda安装目录envs文件下找到
conda create -n env_name python=2.7 

#同时安装必要的包
conda create -n env_name numpy matplotlib python=2.7 
```
```
切换到新环境
source activate env_name
#退出环境
source deactivate env_name
```
```
#安装package到环境env_name中
conda install -n env_name package_name
#另一个方法：切换到相应环境下，直接安装
```
```
#移除环境
conda remove -n env_name --all
#或者直接进入conda安装目录envs文件里直接删除相应的环境文件夹
```
```
#更新包
conda update numpy
#卸载包
conda remove numpy
#卸载指定环境下的包
conda remove --name env_name package_name
```

## conda + keras + tensorflow + gpu
```
# 几个注意的地方：电脑安装的cuda版本和cudnn版本，可适应的tensorflow相应的版本需要去查一查
# 可以在这查找：https://www.tensorflow.org/install/install_sources#ConfigureInstallation
conda create -n keras_tf_gpu_env tensorflow-gpu=1.4
source activate keras_tf_gpu_env
conda install keras-gpu
```
- 默认gpu
```
import tensorflow as tf
# Creates a graph.
a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
c = tf.matmul(a, b)
# Creates a session with log_device_placement set to True.
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
# Runs the op.
print(sess.run(c))
```
- 手动设置gpu与cpu
```
# Creates a graph.
import tensorflow as tf
with tf.device('/cpu:0'):
  a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
  b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
c = tf.matmul(a, b)
# Creates a session with log_device_placement set to True.
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
# Runs the op.
print(sess.run(c))
```
- Reference: https://www.tensorflow.org/programmers_guide/using_gpu

## [Virtualenv](https://virtualenv.pypa.io/en/stable/)--另一个创建一个虚拟化运行环境工具

### 基本操作

```
#创建虚拟环境文件目录
virtualenv env-name

#启用
source env-name/bin/activate
#关闭
deactivate
```
