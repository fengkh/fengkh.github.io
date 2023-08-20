---
title: Openslide安装避坑指南
tags: 
  - Python packages
toc: true
categories: Coding
description: python包安装踩坑路
abbrlink: 8f5ba4dd
date: 2021-11-09 22:29:47
---

### 一、Openslide简介



1. Openslide是一个python的包。

   

2. Openslide包是用来打开并处理医学图片的，类似.ndpi或者.mrxs这种全扫描片，目前都是通过openslide来进行处理的。

   

### 二、openslide安装



1. 不能直接pip，要先下载[二进制文件](https://openslide.org/download/)；

   

   <img src='https://imagebed-2jk.pages.dev/img/Openslide安装避坑指南/openslide安装避坑指南_1_1.jpg' width='70%'>

   

2. 把下载好的二进制文件解压到自己喜欢的目录（如果是Anaconda环境，需要解压到Anaconda的Library目录下）；

   

3. 然后配置环境变量，在系统变量Path里新建如下两个路径；

   

   <img src='https://imagebed-2jk.pages.dev/img/Openslide安装避坑指南/openslide安装避坑指南_1_2.jpg' width='40%'>

   

   比如解压在D盘的根目录下，则新建的路径如下：

   ```
   D:\openslide-win64-20171122\openslide-win64-20171122\bin
   D:\openslide-win64-20171122\openslide-win64-20171122\lib
   ```

   

4. Pip安装Openslide包，直接使用以下指令

   ```python
   pip install openslide-python
   ```

   

5. 这里我们需要到openslide的安装目录下，修改名为lowlevel.py的文件，添加如下代码：

   

   ```python
   import os
   os.environ['PATH'] = "#这里为openslide安装目录的bin目录" + ";" + os.environ['PATH']
   ```

   

   注意：这里的openslide安装目录并不是上文第二步中提到的解压目录，而是第四步中pip的安装目录。非Anaconda环境的目录在`C:\Users\username\AppData\Roaming\Python\Python37\site-packages\openslide` 下；Anaconda环境的目录在Anaconda安装目录的`.\envs\envirname\Lib\site-packages\openslide`。

   更新：这里如果用的是python3.8及以上的版本，以上代码需要更改，因为3.8以上版本的读取路径方式发生了修改。

   

6. 至此，Openslide安装就完成了，import openslide成功！

   

   备注：如果第六步报错，删掉lowlevel.py里的`from . import _convert`之后再次尝试import openslide便可成功。

   

   

   
