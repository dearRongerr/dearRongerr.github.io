---
draft: false
lightgallery: false
password:
message:
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg
license:
comment: true
weight: 0
hiddenFromHomePage: false
hiddenFromSearch: false
hiddenFromRelated: false
hiddenFromFeed: false
resources:
  - name: featured-image
    src: featured-image.jpg
  - name: featured-image-preview
    src: featured-image-preview.jpg
toc: true
math: true
title: '备忘录 | Git基础命令'
subtitle:
date: 2025-11-20T11:00:55+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib
tags:
  - Git
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱'
categories:
  - '⛄好用的代码片'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->

## 如果所有的环境都是干净的环境

### 初始本地仓库

```bash
git init
```

### opt | 添加 readme

> 自动生成 github 的 main 分支 

```bash
git add README.md
```



### add & commit

```bash
git add .
git commit -m "今天的第一次提交"
```

### 重命名当前分支

> 为什么要做这一步：默认情况下，Git 初始化时会创建一个名为 master 的分支。这个命令将默认分支从 master 重命名为 main，以符合现代的命名习惯。

```bash
git branch -M main
```

### 初始化远程仓库

> 区分本地仓库和远程仓库

```bash
git remote add origin {你的github仓库地址}
git remote add origin 
```

### 将本地分支推送到远程仓库

> 将本地的 main 分支推送到远程仓库 origin，并设置上游分支。

```bash
git push -u origin main
```

## 如果用的是别人的环境

### 删除本地仓库

```bash
rm -rf .git
```

### 删除本地仓库与远程仓库的链接

```bash
git remote remove origin
```



## 好用的命令

### 查看当前本地仓库连接的远程仓库

```bash
git remote -v
```

## 关于 token

### 个人 token生成

> 点击个人头像>>settings>>Developer Settings

![image-20251120112104005](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511201345613.png) 

> 选经典

![image-20251120112241441](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511201345614.png)

> note 随便填，中文也行，选作用范围：仓库和工作流

![image-20251120112418704](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511201345616.png) 

> 然后拉到最后，选生成就行，记得保存好个人token，只生成一次

### 个人 token使用

> token 不能显式出现在文件中（不知道，反正泄露可能不安全），设置为环境变量更安全

这里，在哪儿使用这个 token，就在哪个仓库操作

> 点击某个仓库>>settings>>secrets and variables>>actions>>new repo secret

![image-20251120113526412](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511201345617.png)

> Name 填大写 英文命名
>
> Secret 把自己生成的个人token 粘贴过来

![image-20251120113728630](https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202511201345618.png)