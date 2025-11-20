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

