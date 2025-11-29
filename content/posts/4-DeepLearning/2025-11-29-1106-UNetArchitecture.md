---
slug: 24196af180
draft: true
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
title: UNet
subtitle:
date: 2025-11-29T11:06:33+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github BugFix Screen SOP

tags:
  - BibTex
  - LaTex
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点' | '草稿'
categories:
  - '草稿'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->
## Bibtex

```latex
@inproceedings{ronneberger2015u,
  title={U-net: Convolutional networks for biomedical image segmentation},
  author={Ronneberger, Olaf and Fischer, Philipp and Brox, Thomas},
  booktitle={International Conference on Medical image computing and computer-assisted intervention},
  pages={234--241},
  year={2015}
}
```



## 引入

U-Net 结构（算法~\ref{alg:U-Net 结构}）是由Ronneberger 等~\cite{ronneberger2015u}于 2015 年提出，率先应用于医学图像分割任务，该网络的突出特点是对称的编码器-解码器结构。编码器通过卷积与池化操作逐步提取图像的深层语义特征，解码器则通过上采样与跳跃连接（Skip Connection）融合编码器不同层级的特征，既保留了图像的细节信息，又实现了语义特征的精准还原。

## 伪代码

```latex
\begin{algorithm}[hbt!]
\caption{U-Net 结构}
\label{alg:U-Net 结构}
\begin{minipage}{0.9\textwidth} % 设置宽度为页面宽度的90%，可按需调整
\small % 字号缩小一号（默认是\normalsize，\small更小，也可用\footnotesize进一步缩小）
\begin{algorithmic}[1]
\Function{ConvBlock}{$x$, $in\_c$, $out\_c$}
    \State $x \gets \text{Conv2d}(x, in\_{channels}, out\_{channels}, k=3, p=1)$ 
    \State $x \gets \text{BatchNorm2d}(\text{ReLU}(x))$
    \State $x \gets \text{Conv2d}(x, out\_{channels}, out\_{channels}, k=3, p=1)$
    \State $x \gets \text{BatchNorm2d}(\text{ReLU}(x))$
    \Return $x$
\EndFunction

\State \textbf{输入:} 图像 $X \in \mathbb{R}^{C \times H \times W}$，标签 $Y$
\State \textbf{输出:} 分割结果 $\hat{Y} \in \mathbb{R}^{num\_classes \times H \times W}$
\State $skip\_connections \gets [\,]$, $in\_channels \gets C$
\For{$out\_channels$ in $[64, 128, 256, 512]$}
    \State $X \gets \text{ConvBlock}(X, in\_channels, out\_channels)$
    \State \text{append}($skip\_connections$, $X$) 
    \State $X \gets \text{MaxPool2d}(X,k=2)$
\EndFor
\State $X \gets \text{ConvBlock}(X, 512, 1024)$
\For{$out\_channels$ in $[512, 256, 128, 64]$}
    \State $X \gets \text{Up-Conv}(X)$
    \State $skip \gets \text{pop}(skip\_connections)$
    \State $X \gets \text{concat}(X, skip)$ 
    \State $X \gets \text{ConvBlock}(X, out\_channels*2, out\_channels)$
\EndFor
\State $logits \gets \text{Conv2d}(X, 64 , num\_classes, k=1)$
\State $\hat{Y} \gets \text{Softmax}(logits)$
\State \Return $\hat{Y}$
\end{algorithmic}
\end{minipage}
\end{algorithm}
```



## 算法描述

> 整体描述

U-net网络结构呈 U 形，由编码器、瓶颈层、解码器三个模块组成。编码器将输入图像压缩为一个高维低分辨率的特征表示。通过卷积层逐步提取图像的局部和全局特征，通过下采样操作逐步减少特征图的空间尺寸，同时增加通道数。编码器最终输出的特征图包含了图像的高级语义信息。解码器将编码器提取的高维特征还原为原始分辨率。通过上采样操作逐步恢复特征图的空间尺寸，为了弥补编码器中丢失的空间细节，通过跳跃连接融合编码器对应的特征图和当前特征图。解码器最终输出的特征图经过一个 $1\times 1$ 的卷积层映射为每个像素点额类别概率。

> 分步骤描述

对于图像 $X$，...


