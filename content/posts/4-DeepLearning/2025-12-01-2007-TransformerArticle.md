---
slug: 16b44130a4
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
title: Transformer 架构
subtitle:
date: 2025-12-01T20:10:28+08:00
# 常用标签
# Hugo Python Shell MacOS VsCode Matplotlib Git Github BugFix Screen SOP

tags:
  - 
# 常用分类
# '📝便签' | '🕷️捉个虫' | '🧚🏻‍♀️深度学习' | '📜文献阅读' | '⛓️‍💥代码复现' | 
# '⛄好用的代码片' | '📈统计学' | '🖇️碎笔论文' | '📮收纳箱' | '🍎来跟我点点点' | '草稿'
categories:
  - '🧚🏻‍♀️深度学习'
  - '草稿'
description:
keywords:
summary:
# See details front matter: https://fixit.lruihao.cn/documentation/content-management/introduction/#front-matter
---

<!--more-->

Transformer 由 Google 团队 Vaswani等于 2017 年提出，率先应用于自然语言处理领域，其结构的突出特点是完全摒弃循环结构和卷积结构，仅用自注意力机制实现全局依赖建模，从而一次性编码超长序列并支持大规模并行训练。

Transformer 的架构如图所示。Transformer 架构具有明确的模块化设计，具有灵活的可扩展范式。模型通过位置编码补充序列的位置信息（Positional Encoding），整体由编码器(Encoder)和解码器(Decoder)构成，两者由 $N$ 个相同的子层堆叠而成，并通过顶部线性 $Softmax$ 输出预测分布。

<img src="https://cdn.jsdelivr.net/gh/dearRongerr/PicGo@main/202512011945857.png" alt="Transformer1" style="zoom:22%;" />



###  位置编码

输入序列首先经过嵌入层进行特征嵌入，实现输入特征的维度对齐， 并与位置编码相加，保留时序顺序。位置编码通常由正弦函数和余弦函数计算，对于序列中的第 $i$个位置，其位置编码的计算如公式所示。


$$
\begin{cases}
PE(i,2k)=\sin(\frac{i}{10000^{\frac{2k}{d}}}) \\
PE(i,2k)=\cos(\frac{i}{10000^{\frac{2k}{d}}}) \\
\end{cases}
$$
其中，$d$ 为嵌入维度，$k$ 为位置编码的维度索引，$i$ 为当前位置的索引。位置编码使得 Transformer在实现并行运算的同时，保留输入序列的位置信息。

### 编码器、解码器

编码器层子接收经过词嵌入和位置嵌入的输入，由多头自注意力层（MultiHead Self Attention,MHSA）、层归一化(Layer Norm)、残差连接(skip connection)构成。其中，多头注意力子层捕捉输入序列的全局依赖关系，层归一化和残差连接稳定训练的梯度。解码器子层在编码器子层的基础上，引入掩码注意力机制和交叉注意力机制。其中，掩码机制通过屏蔽未来信息，确保自回归预测的因果结构；交叉注意力机制接收编码器的最终输出，与每个解码器子层通过多头交叉注意力机制（MultiHead Cross Attention,MHCA）实现特征交互。Transformer 的核心是注意力机制。

注意力机制模仿人类的视觉注意力，其核心思想是计算输入数据中各部分之间的相关性，自动选择重要的信息进行处理。其计算如公式所示。


$$
Attention(Q,K,V)=softmax(\frac{QK^T}{\sqrt{d_k}})V
$$ 
其中，查询(Query,Q)、键(Key,K)、值(Value,V)通过对输入进行线性变换得到。通过缩放点积注意力机制计算注意力权重矩阵，注意力权重矩阵通过计算 $Q$、$K$、$V$ 之间的相似度为每个输入分配权重，首先计算 $Q$ 和 $K$ 之间的点积，然后除以缩放因子 $\sqrt{d_k}$ （其中 $d_k$ 为键向量的梯度 ）避免点积过大，最后通过$softmax$ 实现归一化，得到注意力分数。

多头注意力机制从多个角度学习序列中不同部分之间的相关性，提高了模型的表达能力。具体来说，如公式所示，给定$Q$、$K$、$V$ 矩阵，将其转换为 $h$ 个子查询、子键、子值，然后分别进行注意力机制运算，最后拼接多头，得到最终的权重矩阵。


$$
Multihead(Q,K,V)=Concat(head_1,head_2,..,head_h)W^O
$$


其中，$W^O$ 为输出的线性变换矩阵，第 $i$ 个头的计算公式如下：
$$
head_i=Attention(QW_i^Q,KW_i^K,VW_i^V)
$$
其中，$W_i^Q$、$W_i^K$、$W_i^V$ 为每个头的权重矩阵。


Transformer通过前馈神经网络（Feedforward Neural Network，FFN）得出预测输出。前馈神经网络（如公式 ~ref{}~ 所示）由两个全连接层和一个激活函数组成。第一个全连接层接收解码器的输出并将其映射到高维空间，增强模型对于复杂特征的学习能力。接着通常采用 ReLU 作为激活函数，增强网络的表达能力。最后通过第二个全连接层还原维度实现维度匹配。
$$
FFN(x)=ReLU(xW_1+b_1)W_2+b_2
$$

其中，$x$为 $FFN$ 接收的输入，$W_1$ 、 $W_2$ 为权重矩阵，$b_1$ 、$b_2$ 为偏置项。


### 学以致用

> 本文将 Transformer 的自注意机制和交叉注意力机制引入长期时间序列预测，自注意力机制用于对每个分支分辨率进行特征提取，交叉注意力机制用于将补偿高层分辨率分支的高频季节项信息。
