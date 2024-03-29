---
title: 目标检测之Faster RCNN
tags:
  - 论文阅读
  - 深度学习
categories:
  - Papers
mathjax: true
swiper_index: 5
description: 两阶段目标检测经典算法之一
abbrlink: 451f615a
date: 2023-03-08 16:00:14
---

### 一、简介

该论文作为目标检测中二阶段算法极具代表性的文章，提出了基于anchor的目标检测器，抛弃了传统的通过滑动窗口、SS（Selective Search）等方法生成目标候选框的方法，使用RPN直接生成目标候选框，极大地提升了目标候选框的生成速度。放到如今的2023年，虽然有视觉Transformer的强势冲击，RPN仍然是目标检测领域一个主力军。

[论文传送门](https://arxiv.org/abs/1506.01497)

### 二、网络结构

#### 1. 结构总览

整个Faster RCNN网络的结构可以分为**四个大块**：**backbone、RPN、RoI、Head**。

1. backbone可以任意根据新的好的主干网络进行更换，主要用来进行输入图像的特征提取，现在甚至可以将其替换成基于transformer的ViT、Swim transformer之类的，这里不做过多赘述。
2. RPN（Region proposal network）作用是进行建议区域的提取网络，在特征图中先生成一系列的anchors，然后通过边界框回归等一系列操作，生成最终修正过的anchors。
3. RoI（Region of Interest）作用是将anchors映射回原特征图，并进行最大池化，得到固定长度的特征图。
4. Head，通过将固定长度的特征图进行FC之后进行类别softmax和BBox 回归。

整个网络的前向传播过程也是按照这四个模块的顺序进行传播的，粗略来说就是首先输入一张（一批）图片，经过backbone得到输出为输入图像的特征图，然后将特征图输入到RPN中，得到的输出为特征图的区域建议框（anchor），并且会在这一步将从特征图上选取的一系列区域建议框映射到原特征图的位置中，并在这一步将边缘的anchor的边框与特征图的边框进行对齐（即在边缘的anchor可能出现超出图片边缘的边框，则将图片边缘当做该anchor的新边界）。然后将对应好的anchors作为输入进入RoI层， 得到的输出为固定长度的proposal feature maps。最后就是Head，将proposal feature maps作为输入，经过全连接层，进行分类和边界框回归，得到最终结果，其网络结构如图2.1所示。

<center><img src= 'https://imagebed-2jk.pages.dev/img/2023-03-08-Faster-RCNN阅读笔记/fasterrcnn.png' width ='85%'></center>

<center>图1.1 Faster RCNN结构图</center>

#### 2. Backbone

原文使用到的backbone为VGG16，这里对VGG16不做详细介绍，因为backbone在后续的研究可以被随意更换，Pytorch官方更是实现了Faster RCNN的多个backbone版本，可以在[官方代码](https://github.com/pytorch/vision/blob/main/torchvision/models/detection/faster_rcnn.py)中查看。

#### 3. RPN

RPN是Faster RCNN系列中最重要的一个模块，作者后续的Mask RCNN版本中也仍然沿用了这一设计，RPN的结构如图2.2所示。

<center><img src='https://imagebed-2jk.pages.dev/img/2023-03-08-Faster-RCNN阅读笔记/RPN.png' width='70%'></center>

<center>图3.1 RPN结构图</center>

图2.2中红框的输入是通过backbone提取到的特征图，首先会通过红框中所示的Relu(Conv2d())，产生两个分支，上面的分支用于anchor的产生，并且通过softmax判断哪些anchor是positive哪些是negative的，下面的分支用于计算anchor的BBox regression的偏移量，用来获取精确的proposal。最后的Proposal层是用来综合两个分支以得到最终的proposals，同时会剔除太小的或者超出边界的proposals。

#### 4. RoI

RoI Pooling首先会将Proposal模块得到的proposals框（因为此时的proposals对应的尺寸是M * N）映射到feature map的尺寸，即(M/16) * (N/16)。但是仍然有个问题需要解决：由于Proposal模块产生的proposals的形状太小不一，但是输出需要是固定的长度。在Faster RCNN中是这样实现的：首先将proposals框对应到feature map的部分从内部划分为7*7的网格，此时不同的proposals内部网格对应的实际像素个数是不相同的，如图4.1所示。然后再对每个7 * 7的网格进行最大池化处理生成proposal feature map（尺寸为1*49）。

<center><img src='https://imagebed-2jk.pages.dev/img/2023-03-08-Faster-RCNN阅读笔记/roi.png' width='40%'></center>

<center>图4.1 RoI Pooling示意图</center>

#### 5. Head

Head会利用RoI层获取的proposal feature map经过softmax层之后对目标的类别进行分类，输出cls_prob类别概率向量；同时再次对proposals进行边界框回归，以得到更精确的边界框坐标。

<center><img src='https://imagebed-2jk.pages.dev/img/2023-03-08-Faster-RCNN阅读笔记/head.png' width='60%'></center>

<center>图5.1 Head结构图</center>