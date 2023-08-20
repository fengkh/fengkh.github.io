---
title: Pytorch入门级深度学习框架搭建
tags: 
  - 深度学习
  - Pytorch
toc: true
categories: Coding
description: Pytorch
abbrlink: f700e2b8
swiper_index: 1
date: 2022-10-10 15:29:43
---

# Torch

# 一、教程地址

[PyTorch深度学习快速入门教程（绝对通俗易懂！）【小土堆】_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1hE411t7RN?p=8&spm_id_from=333.1007.top_right_bar_window_history.content.click)

---

# 二、深度学习实战

## （一）Dataset

### 1.引用

from [torch.utils.data](http://torch.utils.data) import Dataset

### 2.必须实现的方法：

| __init__(self):         | 初始化方法，通常用来初始化一些路径参数                       |
| ----------------------- | ------------------------------------------------------------ |
| __getitem__(self, idx): | 获取一个数据的方法。如果为图像数据，通常返回一个img和一个label对象 |
| __len__(self):          | 返回数据集的大小                                             |

### 3.代码

```python
from torch.utils.data import Dataset
import os
from PIL import Image

class MyDataset(Dataset):
    def __init__(self, root_dir, label_dir):
        self.root_dir = root_dir
        self.label_dir = label_dir
        self.path = os.path.join(self.root_dir, self.label_dir)
        self.img_path = os.listdir(self.path)

    def __getitem__(self, idx):
        label = self.label_dir
        img = Image.open(os.path.join(self.path, self.img_path[idx]))
        return img, label

    def __len__(self):
        return len(self.img_path)

if __name__ == '__main__':
    root_dir = 'hymenoptera_data/train'
    ants_label_dir = 'ants'
    bees_label_dir = 'bees'
    ants_dataset = MyDataset(root_dir, ants_label_dir)
    bees_dataset = MyDataset(root_dir, bees_label_dir)
```

## （二）Tensorboard

### 1.引用

from torch.utils.tensorboard import SummaryWritter

### 2.常用的方法

| 方法名                                                 | 备注                                                         |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| add_scalar(self,tag,scalar_value,global_step)          | 这是常用的用来写数据的方法。ag是图名，scalar_value是纵坐标，global_step是横坐标 |
| add_image(self,tag,img_tensor,global_step,dataformats) | 这是用来添加图片的方法。img_tensor需要是numpy或者tensor类型,dataformats用来指定numpy类型图片的格式是‘HWC’还是’CHW’ |
| close()                                                | 关闭，通常是必须的代码                                       |

### 3.使用

控制台使用代码：tensorboard —logdir=logs —port=6006

如果是服务器中有很多训练的话，可以使用—port指定不同的端口来显示。

### 4.代码

```python
from torch.utils.tensorboard import SummaryWriter
import numpy as np
from PIL import Image

tb = SummaryWriter('log')
path = 'hymenoptera_data/train/bees/21399619_3e61e5bb6f.jpg'
img = Image.open(path)
img = np.array(img)
print(img.shape)
tb.add_image('test2', img, 1, dataformats='HWC')
# for i in range(10000):
#     tb.add_scalar('y=x+x^2', i + i * i, i)
tb.close()
```

## （三）Transform

### 1.引用

from torchvision import transforms

### 2.含义及使用

tensor：是一个包装了训练所需参数的一种数据类型 

transforms类似于一个工具箱，里面有很多类，作为工具使用。所以在需要使用一个工具时，需要先实例化一个工具的对象然后再使用。比如ToTensor的使用：

```python
from torchvision import transforms
from PIL import Image

tensor_trans = transforms.ToTensor()
img_tensor = tensor_trans(Image.open(''))
```



<img src='https://imagebed-2jk.pages.dev/img/Pytorch/Pytorch_Untitled.png' width='70%'>

​																											transfroms工具的使用流程

### 3.常见的transforms

（1）图片打开方式

| Python包                           | 打开方法     | 数据类型       |
| ---------------------------------- | ------------ | -------------- |
| from PIL import Image              | Image.open() | JPEGImgFile    |
| from torchvision import transforms | ToTensor()   | tensor         |
| import cv2                         | cv2.imread() | numpy(ndarray) |

（2）transforms中的类

| 类名       | 参数                                                | 作用                                                   |
| ---------- | --------------------------------------------------- | ------------------------------------------------------ |
| Compose    | 一个列表[transforms1,transforms2,...]               | 组合transforms，可看成串联操作                         |
| ToTensor   | pic: PIL or numpy.ndarray                           | 把PIL格式或者numpy格式的图像转换成一个tensor类型的数据 |
| Normalize  | mean: [float,float,float]  std: [float,float,float] |                                                        |
| ToPILImage |                                                     |                                                        |
| Resize     | object: 可以为(512,512)                             | 把PIL图像resize到给定的尺寸                            |
| RandomCrop | object: 可以为(512,512)                             | 在PIL中随机裁剪size尺寸的图片并返回                    |

## （四）公共数据集

### 1.引用

import torchvision

### 2.使用

torchvision.dataset.数据集名称

包含参数：root:str    train=True|False  download=True|False  transform=(这里是需要自己预先设定好transforms操作的)

## （五）Dataloader

### 1.实例代码

```python
import torchvision
from torch.utils.data import DataLoader

test_data = torchvision.datasets.CIFAR10(root='./dataset/CIFAR10', train=False,
                                         transform=torchvision.transforms.ToTensor(), download=True)
test_loader = DataLoader(dataset=test_data, batch_size=4, shuffle=True, num_workers=0, drop_last=False)
img, target = test_data[0]
print(img.shape)
print(target)
```

### 2.理解

dataloader可以理解为一个取数据的容器，按照batch_size的大小从数据集里每次打包一个batch的数据，并且是分别将img和target分开打包的。

## （六）nn.Module

### 1.理解

可以理解为一个网络模板，只需要进行一定的修改，便可以形成自己的网络模型

### 2.用法

通常是定义自己的modul类并继承nn.Module类，然后实现其中的方法

具体的Module类可以参看下面的官方文档：

[Module - PyTorch 1.11.0 documentation](https://pytorch.org/docs/stable/generated/torch.nn.Module.html#module)

### 3.代码

**二维卷积**：

```python
import torch
import torchvision
from torch.utils.data import DataLoader
from torch import nn
from torch.utils.tensorboard import SummaryWriter

dataset = torchvision.datasets.CIFAR10('./dataset/CIFAR10', train=False, transform=torchvision.transforms.ToTensor(),
                                       download=True)
dataloader = DataLoader(dataset, batch_size=64, shuffle=True, num_workers=0)

class MyModule(nn.Module):
    def __init__(self):
        super(MyModule, self).__init__()
        self.conv1 = nn.Conv2d(in_channels=3, out_channels=6, kernel_size=3, stride=1, padding=0)

    def forward(self, input):
        output = self.conv1(input)
        return output

module = MyModule()
writer = SummaryWriter('logs')
step = 1
# print(output)
for data in dataloader:
    imgs, target = data
    output = module(imgs)
    # print(output.shape)
    writer.add_images('input', imgs, step)
    output = torch.reshape(output, (-1, 3, 30, 30))
    writer.add_images('output', output, step)
    step += 1
writer.close()
```

**最大池化：减少参数的同时保留特征**

重要参数：ceil_model、kernel_size 

torch.tensor 生成矩阵的时候，记得加上dtype=torch.float32这个参数，不然就被识别成Long型无法处理。

**模型的保存和提取：**

torch.save()

torch.load()

**模型的参数更新：**

先选择优化器，optimizer，然后optimizer.zero_grad()

然后求LOSS，对求完的LOSS进行backward()

然后optimizer.step()
