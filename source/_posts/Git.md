---
title: Git入门教程
tags: 
  - 实用工具
  - Git
toc: true
categories: Coding
description: git和github等仓库管理
abbrlink: 29207ea7
date: 2022-10-10 15:34:24
---

# Git

## 一、基本原理

教程来源：

[Git工作流和核心原理 | GitHub基本操作 | VS Code里使用Git和关联GitHub_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1r3411F7kn?spm_id_from=333.337.search-card.all.click&vd_source=3aa92f173cf789aed74021ca69519fcd)



<center><img src='https://imagebed-2jk.pages.dev/img/Git_Untitled.png' width='80%'></center>



## 二、使用教程

### **本地Git使用命令**

| git config —global user.name “用户名” | 设置用户名                  |
| ------------------------------------- | --------------------------- |
| git config —global user.email “邮箱”  | 设置邮箱                    |
| git init                              | 初始化文件夹为git文件工作区 |
| git status                            | 查看当前工作区的状态        |

| git add 文件名              | 添加文件                                                     |
| --------------------------- | ------------------------------------------------------------ |
| git commit -m “版本信息”    | 提交文件                                                     |
| git log                     | 查看往期版本                                                 |
| git commit -a -m “版本信息“ | 光速提交                                                     |
| touch .gitignore            | 创建要忽略的文件夹，即不会被添加到工作区的文件。需要把不提交的文件名写到.gitignore文件里 |

| git branch 分支名      | 创建新的分支                       |
| ---------------------- | ---------------------------------- |
| git checkout 分支名    | 切换到指定分支                     |
| git branch -d 分支名   | 删除分支                           |
| git checkout -b 分支名 | 创建新的分支并切换到新分支         |
| git merge 分支名       | 把分支名的分支合并到当前所处的分支 |

### Github结合git使用

| git clone HTTPS                        | 从github下载到本地                                           |
| -------------------------------------- | ------------------------------------------------------------ |
| git remote -v                          | 查看当前本地仓库和哪些远程仓库有联系                         |
| git remote add origin git@github.com…. | 把本地仓库与远程仓库关联                                     |
| git push -u origin master              |                                                              |
| git fetch                              | 拉远程仓库到暂存                                             |
| git pull                               |                                                              |
| git rm -r -n 文件夹 —cached            | 删除远程仓库里的文件夹但不删除本地的，如果不加—cached，本地里的也会删掉。如果不加-n会直接删掉，加了的话会先预览删除的文件。 |
