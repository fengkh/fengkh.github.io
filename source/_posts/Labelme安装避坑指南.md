---
title: Labelme安装避坑指南
tags: 
  - Python packages
toc: true
categories: Coding
description: python包安装踩坑路
abbrlink: b8ce5541
date: 2021-11-09 22:30:05
---

### 一、Anaconda安装



1. Anaconda[下载](https://www.anaconda.com/products/individual)对应电脑版本的，windows10系统的直接点这个就可以下载

   <img src="https://imagebed-2jk.pages.dev/img/Labelme安装避坑指南/labelme安装避坑指南_1_1.png" width = "80%">



2. 下载完成之后运行安装程序，按照如下步骤点击安装。（大概需要2.6G空间，安装路径默认就好）

   依次点击：

   

   ```
   Next>  
   
   I Agree
   
   All Users  Next>
   
   Next>
   这里出现两个勾选项，全部勾上  Install
   
   然后安装完成之后的Finish界面两个勾选项取消掉，然后单击Finish
   ```

   

3. 到这里，Anaconda安装完成，开始写环境变量，在左下角的搜索栏搜索“环境变量”并打开

   <img src="https://imagebed-2jk.pages.dev/img/Labelme安装避坑指南/labelme安装避坑指南_1_2.png" width = "50%">

   

<img src="https://imagebed-2jk.pages.dev/img/Labelme安装避坑指南/labelme安装避坑指南_1_3.png" width = "45%">



点击“环境变量”，找到如下图所示的系统变量Path并双击打开

<img src="https://imagebed-2jk.pages.dev/img/Labelme安装避坑指南/labelme安装避坑指南_1_4.png" width = "45%">



再点击新建，如下图内容输入下面的内容`C:\ProgramData\Anaconda3\Scripts`点击确定，这里就配置好了

<img src="https://imagebed-2jk.pages.dev/img/Labelme安装避坑指南/labelme安装避坑指南_1_5.png" width = "45%">



### 二、创建虚拟环境并安装Labelme



1. 进入命令行，输入conda检查Anaconda是否安装成功，出现如下界面表示正确，进行下一步，否则重新安装Anaconda。

<img src='https://imagebed-2jk.pages.dev/img/Labelme安装避坑指南/labelme安装避坑指南_2_1.png' width='80%'>

2. 输入`conda create -n labelme python=3.6`并回车，这里提示[y/n]，输入y然后回车。	



3. 依次输入如下命令，完成labelme安装：

   ```
   conda activate labelme
   
   pip install pyqt5
   
   pip install labelme
   
   ```



4. 至此，Labelme安装完成，如需使用按照如下步骤：

   

   （1）Win+R 输入cmd打开命令行窗口；

   （2）输入`conda activate labelme`；

   （3）输入`labelme`；



### 三、json转换数据集



以上步骤能完成Labelme的安装和使用，如需转换json文件为数据集，还需要注意避如下坑：



1. 首先，labelme标准安装版本一次只能转换一个json文件，如果需要批量转换，需要参考其他操作。



2. 转换的操作如下：

   

   （1）进入到`.\Anaconda3\envs\labelme\Lib\site-packages\labelme\cli`目录下;

   （2）在该目录进入cmd命令行；

   （3）输入`labelme_json_to_dataset.exe <你的json文件路径>`；



3. 某些版本的labelme可能会导致转换之后的文件缺少info.yaml文件，需要做如下修改：

   

   （1）进入如下目录：`.\Anaconda3\envs\py36_labelme\Lib\site-packages\labelme\cli`，找到名为json_to_dataset.py文件；

   （2）在json_to_dataset.py中添加如下内容：

   

   ```python
       PIL.Image.fromarray(img).save(osp.join(out_dir, 'img.png'))
       utils.lblsave(osp.join(out_dir, 'label.png'), lbl)
       PIL.Image.fromarray(lbl_viz).save(osp.join(out_dir, 'label_viz.png'))
       with open(osp.join(out_dir, 'label_names.txt'), 'w') as f:
           for lbl_name in label_names:
               f.write(lbl_name + '\n')
   # 缺少的部分，需要添加
       logger.warning('info.yaml is being replaced by label_names.txt')
       info = dict(label_names=label_names)
       with open(osp.join(out_dir, 'info.yaml'), 'w') as f:
           yaml.safe_dump(info, f, default_flow_style=False)
   
   	logger.info('Saved to: {}'.format(out_dir))
       
   if __name__ == '__main__':
       main()
   ```

   （3）重新按照2中操作转换json文件即可；
