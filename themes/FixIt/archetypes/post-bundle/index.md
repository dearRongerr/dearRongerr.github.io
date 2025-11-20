---
title: {{ replace .TranslationBaseName "-" " " | title }}
subtitle:
date: {{ .Date }}
slug: {{ substr .File.UniqueID 0 7 }}
hiddenFromHomePage: false
hiddenFromSearch: false
hiddenFromRelated: false
hiddenFromFeed: false
summary:
resources:
  - name: featured-image
    src: featured-image.jpg
  - name: featured-image-preview
    src: featured-image-preview.jpg
toc: true
math: true
lightgallery: false
password:
message:
comment: true
weight: 0
license:
author: 
  name: 溶 err
  link:
  email:
  avatar: images/avatar.jpeg # 可能报错
repost:
  enable: true
  url:
# true false
draft: false
description:
keywords:
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib
tags:
  - draft
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱'
categories:
  - '📝便签'


# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->

<!-- Place resource files in the current article directory and reference them using relative paths, like this: `![alt](images/screenshot.jpg)`. -->
