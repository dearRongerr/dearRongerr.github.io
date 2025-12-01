---
slug: 40cb4510bf
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
title:  LaTex
subtitle:
date: 2025-12-01T20:07:50+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github BugFix Screen SOP

tags:
  - LaTex
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点' | '草稿'
categories:
  - '⛄好用的代码片'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

## 公式

## 普通公式

```LaTex
\begin{equation}\label{eq:sip}
    a_1 + b_2 = 3
\end{equation}
```

### 多行公式

```latex
\begin{gather}\label{eq:add}
    1+1=2 \\
    2+2=4 \\
    3+3=6 \nonumber
\end{gather}
```


$$
\begin{gather}\label{eq:add}
    1+1=2 \\
    2+2=4 \\
    3+3=6 \nonumber
\end{gather}
$$

### 多情况公式

```latex
\begin{equation}\label{eq:multi}
    \mathbf{X}=
    \begin{cases}
        k_n \quad & n \ = \ 1  \\
        \mathbf{X}_n \ = \mathbf{X}_{n-1}\ +\ (k_n-1)\times S_{n-1};
        \quad     & n \geq \ 1 \\
    \end{cases}
\end{equation}
```

$$
\begin{equation}\label{eq:multi}
    \mathbf{X}=
    \begin{cases}
        k_n \quad & n \ = \ 1  \\
        \mathbf{X}_n \ = \mathbf{X}_{n-1}\ +\ (k_n-1)\times S_{n-1};
        \quad     & n \geq \ 1 \\
    \end{cases}
\end{equation}
$$



