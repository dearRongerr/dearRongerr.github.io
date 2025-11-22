---
slug: 0e4a49cd77
draft: false
lightgallery: false
password:
message:
repost:
  enable: false
  url:
author: 
  name: 溶 err
  link:
  email:
  avatar:  /images/avatar.jpeg # 可能报错
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
title: 备忘录 | 服务器进程管理
subtitle:
date: 2025-11-22T11:28:17+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github BugFix
tags:
  - Bash
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点'
categories:
  - '⛄好用的代码片'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->




```bash
nvidia-smi
# 进程PID查看正在运行的进程，这里的 Time并非实际的进程运行时间
ps 1386547 

# 查看指定进程的启动时间
ps -o lstart= -p 1386547 

#查看所有进程的启动时间
ps -C python -o pid,lstart,cmd 

 # 查看最新启动的活着的进程
ps -C python -o pid,lstart,cmd --no-headers | grep -v '\[python\] <defunct>' | tail -n 1

#所有活着的进程，去掉僵尸进程
ps -C python -o pid,lstart,cmd --no-headers | grep -v '\[python\] <defunct>' 
```





